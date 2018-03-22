# LLHTTP requests

## Simple request

Input:
```http
OPTIONS /url HTTP/1.1\r\n
Header1: Value1\r\n
Header2:\t Value2\r\n
\r\n
```

Expected:
```log
off=0 message begin
off=8 len=4 span[url]="/url"
off=23 len=7 span[header_field]="Header1"
off=32 len=6 span[header_value]="Value1"
off=40 len=7 span[header_field]="Header2"
off=50 len=6 span[header_value]="Value2"
off=60 headers complete method=6 v=1/1 flags=0 content_length=0
off=60 message complete
```

## Request with method starting on H

There's a optimization in `start_req_or_res` that passes execution to
`start_req` when the first character is not `H` (because response must start
with `HTTP/`). However, there're still methods like `HEAD` that should get
to `start_req`. Verify that it still works after optimization.

Input:
```http
HEAD /url HTTP/1.1\r\n
\r\n
```

Expected:
```log
off=0 message begin
off=5 len=4 span[url]="/url"
off=22 headers complete method=2 v=1/1 flags=0 content_length=0
off=22 message complete
```