---
layout: post
title:  "Microservices abilities"
summary: "Microservices abilities"
author: thanhtinhpas1
date: '2023-05-28 14:35:23 +0530'
category: microservice
thumbnail: https://s3-ap-southeast-1.amazonaws.com/kipalog.com/dzww7e7j8r_image.png
keywords: Microservices abilities
permalink: /blog/microservices-abilities
usemathjax: true
---

## Các tính chất trong Microservice (Phần 1)
Bài viết trích dẫn từ [System Prime](https://github.com/donnemartin/system-design-primer) 
Trong bài viết có một số từ chuyên ngành như performance, scability, latency, ... mình xin giữ nguyên mẫu để đảm bảo tính đúng đắn của bài trích dẫn.

## Performance vs Scability

Một service có tính mở rộng nếu hiệu năng của nó tăng lên tương xứng với những resource đã được thêm vào. Nói chung, sự tăng hiệu năng có nghĩa là phục vụ được nhiều tác vụ hơn, nhưng cũng đồng thời có thể kiểm soát được từng ấy tác vụ khi dữ liệu tăng dần lên. Nói cách khác:
* Nếu chúng ta có 1 vấn đề về hiệu năng, hệ thống sẽ chậm đi cho chỉ 1 người dùng nói riêng.
* Nếu chúng ta có 1 vấn đề về tính mở rộng, hệ thống tuy nhanh cho 1 người dùng nói riêng, nhưng sẽ chậm dưới áp lực nhiều người dùng.

## Latency vs throughput
**Latency** là thời gian để thực hiện một vài hành động hoặc vận dụng một vài kết quả. Trong khi đó, **throughput** là số hành động, hoặc kết quả trong một đơn vị thời gian.

Nói chung, chúng ta nên hướng đến tối đa **thourghput** với một **latency** chấp nhận được.
Tham khảo: https://community.cadence.com/cadence_blogs_8/b/sd/posts/understanding-latency-vs-throughput

## Consistency pattern
Với nhiều bản sao chép của dữ liệu, chúng ta đối mặt với vấn đề làm cách nào có thể đảm bảo người dùng tiếp cận với dữ liệu có tính đúng đắn cao nhất. 
> Gợi lại **[Cap theorem](https://robertgreiner.com/cap-theorem-revisited/)**, mỗi lượt đọc dữ liệu sẽ nhận dữ liệu gần nhất hoặc chỉ là lỗi.

![Cap theorem](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/fpvbtqpqu8_image.png)

### Week consistency
Sau 1 tác vụ ghi, những tác vụ đọc có thể hoặc không nhìn thấy dữ liệu vừa ghi.

Cách tiếp cận này có thể thấy trong hệ thống ví như sử dụng [memcached](https://memcached.org/). Week consistency hoạt động tốt trong môi trường thời gian thực (real time) ví dụ như VoIP (nghe gọi điện thoại), video chat, và trò chơi thời gian thực multi players. Ví dụ, trong một cuộc gọi điện thoại và chúng ta mất kết nối trong vài giây, khi chúng ta có gắng kết nối, chúng ta sẽ không nghe được người bên kia nói gì trong thời gian mất kết nối.

### Eventual consistency
Sau 1 tác vụ ghi, những tác vụ đọc sẽ thấy dữ liệu ghi tuần tự (có thể chỉ trong vài miliseconds). Dữ liệu sẽ được sao chép [bất đồng bộ](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-is-the-main-difference).
Cách tiếp cận này chúng ta có thể thấy trong các hệ thống như [DNS ](https://vi.wikipedia.org/wiki/H%E1%BB%87_th%E1%BB%91ng_ph%C3%A2n_gi%E1%BA%A3i_t%C3%AAn_mi%E1%BB%81n) hay Email. Eventual concistency hoạt động tốt trong các hệ thống đòi hỏi tính khả dụng cao (high avaibility)

### Strong consistency
Sau 1 tác vụ ghi, tác vụ đọc sẽ ngay lập tức thấy dữ liệu vừa ghi. Dữ liệu được nhân bản đồng bộ (synchronously).
Cách tiếp cận này thường được thấy trong file systems hoặc [RDBMSes](https://en.wikipedia.org/wiki/Relational_database#RDBMS).

Tham khảo: https://snarfed.org/transactions_across_datacenters_io.html

## Availability patterns
Sẽ có 2 mẫu hành vi (patterns) hỗ trợ tính khả dụng cao (high availability): **fail-over** và **replication**.

### Fail-over
#### Active-passive
Với mẫu hành vi active-passive fail-over, hearbeats được gửi giữa máy chủ active và passive (máy chủ bị động và máy chủ chủ động). Một khi heartbeat bị gián đoạn, máy chủ bị động sẽ thay quyền máy chủ chủ động tiếp tục phục vụ trên IP address đã có.

Thời gian gián đoạn (downtime) được xác định đến khi nào máy chủ bị động sẵn sàng để phục vụ trong **hot standby** hoặc **[cold standby](https://www.techopedia.com/definition/999/cold-standby)**. Lưu ý, chỉ có máy chủ chủ động mới có thể xử lý lượng traffic gửi đến.

Active-passive fail-over pattern có thể được hiện thực hóa bởi mô hình [master-slave](https://en.wikipedia.org/wiki/Master/slave_(technology)).

#### Active-active
Trong active-active, cả 2 máy chủ đều xử lý lương traffic gửi đến, lượng tải được trải ra cho 2 máy chủ.
Nếu cả 2 máy chủ đều public, DNS cần biết địa chỉ IP publics của cả 2. Nếu 2 máy chủ chỉ nội bộ (internal-facing), logic trên ứng dụng cần biết được cả 2 máy chủ này.
Active-active pattern có thể hiện thực bằng mô hình master-master.

#### Bất lợi của fail-over
* Mô hình fail-over cần nhiều phần cứng và có độ phức tạp cao
* Có khả năng bị mất dữ liệu nếu active system xảy ra sự cố trước khi dữ liệu được nhân bản sang passive.

### Replication
#### Master slave replication
Những máy chủ master sẽ ghi và đọc, sau đó sao chép dữ liệu sang một hoặc nhiều máy chủ slaves, những máy chủ này chỉ có quyền đọc. Những máy chủ slaves cũng có thể nhân rộng ra các máy chủ slaves của riêng mình. Nếu máy chủ master offline, hệ thống vẫn có thể tiếp tục nhưng chỉ có thể đọc mà không thể ghi đến khi một máy chủ slave được chỉ định thay thế máy chủ master.

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/dzww7e7j8r_image.png)

Tham khảo https://www.slideshare.net/jboner/scalability-availability-stability-patterns/

Bất lợi của master slave là cần có logic được viết để promote 1 máy chủ slave lên làm master

#### Master master replication
Cả 2 máy chủ master đều có thể đọc và ghi, đồng thời tương tác với nhác để có thể phục vụ tác vụ viết. Nếu 1 máy chủ master bị sự cố, hệ thống vẫn có thể tiếp tục với cả 2 tác vụ đọc và ghi.
![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/ib7p8e9x5b_image.png)

**Bất lợi của master master replication**
* Cần có 1 bộ cân bằng tải ([load balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing))) hoặc cần viết logic trong ứng dụng để có thể xác định máy chủ để ghi.
* Hầu hết các hệ thống master-master đều mất tính đúng đắn (loosely consistent, 1 trong 4 tính chất [ACID](https://vi.wikipedia.org/wiki/ACID)) hoặc phải tăng **latency** để đồng bộ kịp thời dữ liệu.
* Xung độ sẽ diễn ra thường xuyên khi viết nếu có nhiều máy chủ master được thêm vào, đồng thời dẫn đến tăng latency.


#### Bất lợi của mô hình Replication
* Có khả năng mất dữ liệu nếu máy chủ master xảy ra sự cố trước khi dữ liệu được sao chép sang máy chủ khác.
* Tác vụ ghi cần thực hiện lại nhiều lần để phục vụ tác vụ đọc, nếu có nhiều tác vụ ghi có thể cản trở quá trình đọc của hệ thống.
* Càng nhiều máy chủ slave, càng nhiều dữ liệu cần sao chép, dẫn tới giật lag.
* Trong một số hệ thống, tác vụ ghi có thể được spawn ở nhiều thread, nhưng tác vụ đọc thì chỉ hỗ trợ 1 tác vụ đọc trong 1 thread.
* Replication cần nhiều phần cứng hơn

Tham khảo: https://en.wikipedia.org/wiki/Multi-master_replication