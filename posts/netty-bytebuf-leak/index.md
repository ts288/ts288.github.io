---
layout: article
title: "Netty ByteBuf leak"
permalink: /posts/netty-bytebuf-leak/
category: spring
topic: webflux-reactor
list_order: 3
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/2d83abb1a94d80e9bcd3fc2b6e58c7e9?pvs=204)

# Netty ByteBuf leak

- 해제하지 않은 ByteBuf 가 GC 의 대상이 될 때 발생하는 경고
  - <b>Q. GC 대상이 되었다 == 이걸 참조하는 곳이 없다 → 그럼 지워도 되는 거 아님?</b>
    - 일반 java 객체면 맞음. 근데 bytebuf 가 사용하는 메모리는 netty 가 참조 카운팅(refCnt) 으로 관리함
    - jvm 은 참조 관계를 추적하지만, netty 는 명시적인 retian()/release() 호출로 카운터를 변경함
    - 그래서 JVM 이 참조 관계 없다고 GC 에서 지워버리면 안됨. 왜냐면 netty 는 명시적인 release() 호출이 없었기 때문에 아직 살아있다고 판정하기 때문
    - 따라서 JVM 은 참조 관계가 없어진 ByteBuf 객체를 GC 로 회수할 수 있지만, Netty 는 release() 가 호출되지 않았기 때문에 refCnt 가 여전히 1 인 상태임.
    - 이 상태에서 객체가 사라지면 Netty 의 메모리 반환 절차가 수행되지 않아 메모리 누수가 발생할 수 있음. 특히 Pooled ByteBuf 는 해당 메모리 영역을 풀에 반환하지 못할 수 있음.
    - 여기서 알 수 있는 건, refCnt 는 실제 참조 개수와 다를 수 있다는 것!
- DataBuffer.join 같은 걸 사용했을 때는 반드시 명시적으로 메모리를 release 해주어야 한다.

```javascript
class RequestInboundLoggingDecorator(
    private val delegate: ServerHttpRequest,
    private val exchange: ServerWebExchange,
    private val cid: String,
) : ServerHttpRequestDecorator(delegate) {
    override fun getBody(): Flux<DataBuffer> {
        // request body 스트림을 하나의 DataBuffer로 합쳐서 처리하는 방식
        // DataBufferUtils.join 으로 body 를 모두 소비한 뒤, 새 Flux 를 만들어 downstream 으로 전달
        return DataBufferUtils.join(super.getBody())
            // DataBufferUtils.join() 은 Mono 를 반환하므로 Flux 로 변환하기 위해 flatMapMany 사용
            .flatMapMany { dataBuffer ->
                try {
                    // DataBuffer 는 read 시 내부 read index 가 변경되어 재사용이 불가능함
                    // 이 구현은 byte 배열로 복사 후 새 DataBuffer 를 생성하므로 downstream 에서 안전하게 읽을 수 있음
                    val totalBytes = ByteArray(dataBuffer.readableByteCount())
                    dataBuffer.read(totalBytes)

                    val chunk = String(totalBytes, Charsets.UTF_8)
                    log.info {
                        "[$cid] Incoming request\n" +
                                "method : ${delegate.method}\n" +
                                "path : ${delegate.path}\n" +
                                "body : \n$chunk"
                    }

                    val outBuffer = exchange.response.bufferFactory().wrap(totalBytes)
                    Flux.just(outBuffer)
                } finally {
                    // DataBufferUtils.join() 으로 생성된 DataBuffer 는 자동으로 해제되지 않으므로
                    // read() 로 소비 후 Netty ByteBuf leak 방지를 위해 명시적으로 release 필요
                    DataBufferUtils.release(dataBuffer)
                }
            }
    }
}

private val log = KotlinLogging.logger {}
```
