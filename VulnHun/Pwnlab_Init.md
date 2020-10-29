# Pwnlab:Init

## Initial Scan
```
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: PwnLab Intranet Image Hosting
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          34587/udp   status
|   100024  1          38958/tcp6  status
|   100024  1          40592/udp6  status
|_  100024  1          47292/tcp   status
3306/tcp  open  mysql   MySQL 5.5.47-0+deb8u1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.47-0+deb8u1
|   Thread ID: 40
|   Capabilities flags: 63487
|   Some Capabilities: Support41Auth, ConnectWithDatabase, SupportsTransactions, LongPassword, Speaks41ProtocolOld, IgnoreSigpipes, ODBCClient, InteractiveClient, Speaks41ProtocolNew, SupportsLoadDataLocal, DontAllowDatabaseTableColumn, IgnoreSpaceBeforeParenthesis, SupportsCompression, FoundRows, LongColumnFlag, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: #L8nFO<{zrNm[aUdwIFa
|_  Auth Plugin Name: mysql_native_password
47292/tcp open  status  1 (RPC #100024)
```
