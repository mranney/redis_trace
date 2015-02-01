# redis_trace

Decode redis protocol from packet capture to better understand who is using your redis server and what they are doing with it.

## Example

```
$ sudo ./redis_trace -f "port 6379" -i lo0
Listening on lo0
03:12:36.562 127.0.0.1:61318 -> 127.0.0.1:6379 TCP start 
03:12:36.568 127.0.0.1:6379 -> 127.0.0.1:61318 1/1 get [foo] -> bar 0.092ms
03:12:36.572 127.0.0.1:6379 -> 127.0.0.1:61318 2/2 keys [*] -> [a key, foo_rand000000000000, foo, foo2] 0.104ms
03:12:36.579 127.0.0.1:6379 -> 127.0.0.1:61318 3/3 get [foo2] -> bar2 0.087ms
03:12:36.593 127.0.0.1:61318 -> 127.0.0.1:6379 TCP end 31.564s 3 commands 0.10 req/sec
```

This program may end up being useful as is, but I hope you'll find it useful to do other types of analysis of
the redis protocol. The protocol is decoded as a JavaScript object from the link layer frame all the way up to
redis commands and replies.
