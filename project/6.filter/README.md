# Filter

```java
@Slf4j
@Component
public class LoggerFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 요청(request)와 응답(response)을 캐시하기 위해 래퍼로 감쌈
        var req = new ContentCachingRequestWrapper((HttpServletRequest) request);
        var res = new ContentCachingResponseWrapper((HttpServletResponse) response);

        // filter chain은 여러 필터를 연속적으로 연결한 구조로
        // 각 필터가 다음 필터를 호출하도록 되어있음
        chain.doFilter(req, res); // 다음 필터나 최종 서블릿으로 요청을 전달

        // 1. 요청 정보 로깅
        var headerNames = req.getHeaderNames();
        var headerValues = new StringBuilder();

        // 1-1. 각 헤더의 키와 값을 로깅
        headerNames.asIterator().forEachRemaining(headerKey -> {
            var headerValue = req.getHeader(headerKey);
            headerValues
                    .append("[")
                    .append(headerKey)
                    .append(" : ")
                    .append(headerValue)
                    .append("] ");
        });

        // 1-2. 요청 본문, URI, 메서드 추출
        var requestBody = new String(req.getContentAsByteArray());
        var uri = req.getRequestURI();
        var method = req.getMethod();

        // 1-3. 요청 정보 로깅
        log.info(">>>>> uri : {}, method : {}, header : {}, body : {}", uri, method, headerValues, requestBody);

        // 2. 응답 정보 로깅
        var responseHeaderValues = new StringBuilder();
        res.getHeaderNames().forEach(headerKey -> {
            var headerValue = res.getHeader(headerKey);
            responseHeaderValues
                    .append("[")
                    .append(headerKey)
                    .append(" : ")
                    .append(headerValue)
                    .append("] ");
        });

        // 2-1. 응답 본문 로깅
        var responseBody = new String(res.getContentAsByteArray());
        log.info("<<<<< uri : {}, method : {}, header : {}, body : {}", uri, method, responseHeaderValues, responseBody);

        // 캐시된 응답 본문을 실제 응답으로 복사
        res.copyBodyToResponse();
    }
}
```

