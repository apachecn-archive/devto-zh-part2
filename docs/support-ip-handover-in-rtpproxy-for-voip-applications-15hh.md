# 支持 VoIP 应用的 rtpproxy 中的 IP 切换

> 原文：<https://dev.to/onmyway133/support-ip-handover-in-rtpproxy-for-voip-applications-15hh>

[![](img/e06ab03295fd975bdb5e76972cbe4e21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U3LZ3yeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ATnIwt7tm6bzDME_G)

如果您使用 [VoIP 应用程序](https://codeburst.io/learning-voip-rtp-and-sip-aka-awesome-pjsip-2035fa02cf88)，尤其是像 pjsip 这样的开源软件，您可能会遇到 [kamalio](https://www.kamailio.org/w/) 和 [rtpproxy](http://www.rtpproxy.org/) 来处理 sip 请求。由于 NAT 穿越的限制，需要 rtpproxy 来绕过 NAT。所有 SIP 握手请求都通过代理服务器，但 rtpproxy 也可以中继语音、视频或任何 RTP 数据流。我玩 rtpproxy 的时候是在版本 [2.0](http://www.rtpproxy.org/post/v2release/) 之前，需要处理 IP 切换。这是指当用户在不同网络之间切换时的场景，例如从 Wifi 切换到 4G，并且他们获得新的 IP。通常这意味着在 SIP 呼叫中结束，但是期望是如果可能的话，我们可以为用户重试并继续呼叫。

这也是我分叉 rtpproxy，加入 IP 切换支持的原因。您可以在 [rtpproxy](https://github.com/onmyway133/rtpproxy) 查看 GitHub repo。

使用 src_cnt 跟踪来自不同地址的连续数据包的数量。当这个数字超过阈值(RTP 为 10，RTCP 为 2)时，我切换到这个新地址

这种方式

*   当客户端从 3G 切换到 Wifi，或者从这个 Wifi 热点切换到另一个 Wifi 热点时，他总是可以更改 IP

*   除非攻击者在 20 毫秒内发送超过 10 个(RTP 阈值)数据包(假设我的客户端每 20 毫秒发送一个数据包)，否则没有攻击的机会

这个想法是借用了[http://www.pjsip.org/pjmedia/docs/html/group_*pj media*_ config . htm](http://www.pjsip.org/pjmedia/docs/html/group__PJMEDIA__CONFIG.htm)

有一个宏 PJMEDIA _ RTP _ NAT _ PROBATION _ CNT。基本上，是的

> *“查看 RTP 包的源地址是否与配置的地址不同，在连续收到几个包后，将 RTP 远程地址切换到源地址。”*

移动客户端现在经常更换 IP，从这些热点换到那些热点。所以如果 rtpproxy 能支持这个特性就更好了。

看一下[https://github . com/onmyway 133/RTP proxy/blob/master/rtpp _ session . h](https://github.com/onmyway133/rtpproxy/blob/master/rtpp_session.h)

```
// IP Handover Count how many consecutive different packets are received, 0 is for callee, 1 is for caller    unsigned int src_count[2]; 
```

Enter fullscreen mode Exit fullscreen mode

以及它在 https://github.com/onmyway133/rtpproxy/blob/master/main.c 的表现

```
static void
rxmit_packets(struct cfg *cf, struct rtpp_session *sp, int ridx,
  double dtime)
{
    int ndrain, i, port;
    struct rtp_packet *packet = NULL;

/* Repeat since we may have several packets queued on the same socket */
    for (ndrain = 0; ndrain < 5; ndrain++) {
 if (packet != NULL)
     rtp_packet_free(packet);

packet = rtp_recv(sp->fds[ridx]);
 if (packet == NULL)
     break;
 packet->laddr = sp->laddr[ridx];
 packet->rport = sp->ports[ridx];
 packet->rtime = dtime;

i = 0;
 // IP Handover do not need canupdate
 // Use src_count
 if (sp->addr[ridx] != NULL) {
     /* Check that the packet is authentic, drop if it isn't */
     if (sp->asymmetric[ridx] == 0) {
  /*
  if (memcmp(sp->addr[ridx], &packet->raddr, packet->rlen) != 0) {
      if (sp->canupdate[ridx] == 0) {
   //
   // Continue, since there could be good packets in
   // queue.
   //
   continue;
      }

      // Signal that an address has to be updated
      rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover Set i 1st ridx %d",ridx);
      i = 1;
  } else if (sp->canupdate[ridx] != 0 &&
    sp->last_update[ridx] != 0 &&
    dtime - sp->last_update[ridx] > UPDATE_WINDOW) 
  {
      sp->canupdate[ridx] = 0;
      rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover Set canupdate to 0 1st ridx %d",ridx);
  }
  */

  if (memcmp(sp->addr[ridx], &packet->raddr, packet->rlen) == 0) { 
   sp->src_count[ridx] = 0;
  } 
  else {
   sp->src_count[ridx]++;
   // IP Handover RTCP packet sends at larger interval, so must use smaller THRESHOLD
   // Check to see if port is odd or even
   if(sp->ports[ridx] % 2 == 0) {
    if(sp->src_count[ridx] >= 10) {
     i = 1;
    } 
   }
   else {
    if(sp->src_count[ridx] >= 2) {
     i = 1;
    }
   }

  }

} else {
  /*
   * For asymmetric clients don't check
   * source port since it may be different.
   */
  rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover We are in asymmetric ridx %d",ridx);
  if (!ishostseq(sp->addr[ridx], sstosa(&packet->raddr)))
      /*
       * Continue, since there could be good packets in
       * queue.
       */
      continue;
     }
     sp->pcount[ridx]++;
 } else {
     sp->pcount[ridx]++;
     sp->addr[ridx] = malloc(packet->rlen);
     if (sp->addr[ridx] == NULL) {
  sp->pcount[3]++;
  rtpp_log_write(RTPP_LOG_ERR, sp->log,
    "can't allocate memory for remote address - "
    "removing session");
  remove_session(cf, GET_RTP(sp));
  /* Break, sp is invalid now */
  break;
     }
     /* Signal that an address have to be updated. */
     rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover Set i 2nd ridx %d",ridx); 
     i = 1;
 }

/*
  * Update recorded address if it's necessary. Set "untrusted address"
  * flag in the session state, so that possible future address updates
  * from that client won't get address changed immediately to some
  * bogus one.
  */
 if (i != 0) {
     sp->untrusted_addr[ridx] = 1;
     memcpy(sp->addr[ridx], &packet->raddr, packet->rlen);

     // IP Handover Do not use canupdate
     // After update, reset src_count
     /*
     if (sp->prev_addr[ridx] == NULL || memcmp(sp->prev_addr[ridx],
       &packet->raddr, packet->rlen) != 0) 
     {
         sp->canupdate[ridx] = 0;
  if(sp->prev_addr[ridx] == NULL)
  {
     rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover prev_addr NULL ridx %d",ridx); 
  }
  rtpp_log_write(RTPP_LOG_ERR, cf->glog, "IP Handover Set canupdate to 0 2nd ridx %d",ridx);
     }
     */

sp->src_count[ridx] = 0;

port = ntohs(satosin(&packet->raddr)->sin_port);

rtpp_log_write(RTPP_LOG_INFO, sp->log,
       "%s's address filled in: %s:%d (%s)",
       (ridx == 0) ? "callee" : "caller",
       addr2char(sstosa(&packet->raddr)), port,
       (sp->rtp == NULL) ? "RTP" : "RTCP");

/*
      * Check if we have updated RTP while RTCP is still
      * empty or contains address that differs from one we
      * used when updating RTP. Try to guess RTCP if so,
      * should be handy for non-NAT'ed clients, and some
      * NATed as well.
      */
     if (sp->rtcp != NULL && (sp->rtcp->addr[ridx] == NULL ||
       !ishostseq(sp->rtcp->addr[ridx], sstosa(&packet->raddr)))) {
  if (sp->rtcp->addr[ridx] == NULL) {
      sp->rtcp->addr[ridx] = malloc(packet->rlen);
      if (sp->rtcp->addr[ridx] == NULL) {
   sp->pcount[3]++;
   rtpp_log_write(RTPP_LOG_ERR, sp->log,
     "can't allocate memory for remote address - "
     "removing session");
   remove_session(cf, sp);
   /* Break, sp is invalid now */
   break;
      }
  }
  memcpy(sp->rtcp->addr[ridx], &packet->raddr, packet->rlen);
  satosin(sp->rtcp->addr[ridx])->sin_port = htons(port + 1);
  /* Use guessed value as the only true one for asymmetric clients */
  sp->rtcp->canupdate[ridx] = NOT(sp->rtcp->asymmetric[ridx]);
  rtpp_log_write(RTPP_LOG_INFO, sp->log, "guessing RTCP port "
    "for %s to be %d",
    (ridx == 0) ? "callee" : "caller", port + 1);
     }
 }

if (sp->resizers[ridx].output_nsamples > 0)
     rtp_resizer_enqueue(&sp->resizers[ridx], &packet);
 if (packet != NULL)
     send_packet(cf, sp, ridx, packet);
    }

if (packet != NULL)
 rtp_packet_free(packet);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些我读过的有用资源

*   [http://stack overflow . com/questions/18200089/how-does-RTP proxy-handle-handover](http://stackoverflow.com/questions/18200089/how-does-rtpproxy-handle-handover)

*   [http://lists . sip-router . org/piper mail/Sr-users/2008-March/062795 . html](http://lists.sip-router.org/pipermail/sr-users/2008-March/062795.html)

*   [http://SourceForge . net/p/sippy/RTP proxy/ci/c 71201d 897 ee 7c 5620 d88 bb 7d 56412 CD 9 DC 3362 a/tree//main . c？diff = 5d 030295 ce 90 F2 f6ff 292 B0 d65 BAE 1d 4318 db 02 a](http://sourceforge.net/p/sippy/rtpproxy/ci/c71201d897ee7c5620d88bb7d56412cd9dc3362a/tree//main.c?diff=5d030295ce90f2f6ff292b0d65bae1d4318db02a)

原帖[https://medium . com/fantageek/support-IP-handover-in-RTP proxy-for-VoIP-applications-DDB 682 a 31453](https://medium.com/fantageek/support-ip-handover-in-rtpproxy-for-voip-applications-ddb682a31453)