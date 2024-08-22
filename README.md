有几种情况都可能会导致mwan+ipv6失效，究其原因都是因为正常ipv6路由被mwan的ipv4负载策略覆盖了(0.0.0.0/0会对ipv6生效，同时::/0也会对ipv4生效，这个互不区分的通配我觉得应该算bug)，可以通过route -A inet6 或者 mwan3 status看的ipv6路由策略，你会发现ipv6被前面的多拨配置影响了，而且是unreachable。所以要么让受影响的v6路由恢复正常状态，要么就让mwan的规则只匹配v4.
解决方法我找到两个：
第一种：
    修改Policy策略的配置，把Last resort(最后策略)选择为 default(use main routing table)，这样会让没匹配到的走默认网关，就不会出现v6不通的情况了。
第二种：
    修改Rules规则，编辑当前使用的规则，把第一项Source address(源地址)填入lan侧子网地址段例如192.168.100.0/24，保存之后匹配规则就只会对ipv4生效而不影响ipv6了
