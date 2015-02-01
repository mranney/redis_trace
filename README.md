# redis_trace

Decode redis protocol from packet capture to better understand who is using your redis server and what they are doing with it.

Install with: `npm -g install redis_trace`

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

This differs from running the redis `MONITOR` command because it does not connect to a redis-server process.
Instead, it watches the packets go by from the network. This allows us to do some pretty interesting things, 
such as break down traffic by client, by key, etc. Also, because we are seeing traffic in both directions,
we can analyze the responses to each command, whereas `MONITOR` only shows the command itself.

This program may end up being useful as is, but I hope you'll find it useful to do other types of analysis of
the redis protocol. The protocol is decoded as a JavaScript object from the link layer frame all the way up to
redis commands and replies.

For example, you can use the `--tcp-verbose` flag to analyze the TCP performance of each connection you see go by:

```
$ sudo ./redis_trace -f "port 6379" -i lo0 --tcp-verbose
Password:
Listening on lo0
03:12:37.295 127.0.0.1:61421 -> 127.0.0.1:6379 TCP start 
03:12:37.295 127.0.0.1:6379 -> 127.0.0.1:61421 1/1 mget [foo, foo2] -> [bar, bar2] 0.081ms
03:12:37.295 127.0.0.1:61421 -> 127.0.0.1:6379 TCP end 0.001s 1 commands 1000.00 req/sec
Set stats for session:  { recv_times: 
   { '2211033425': 0.00006985664367675781,
     '2211033448': 0.00023102760314941406 },
  send_times: { '2244795084': 0.00008106231689453125 },
  send_retrans: {},
  recv_retrans: {},
  connect_duration: 0.00008082389831542969,
  total_time: 0.0005328655242919922,
  send_overhead: 272,
  send_payload: 33,
  send_total: 305,
  recv_overhead: 220,
  recv_payload: 23,
  recv_total: 243 }
```
