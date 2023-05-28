---
layout: post
title:  "Outbox-pattern"
summary: "Outbox-pattern"
author: thanhtinhpas1
date: '2023-05-28 14:35:23 +0530'
category: jekyll
thumbnail: /assets/img/Out%20box%20pattern%20b63cf84af1cb4b44b76c2e07a803f592/Untitled.png
keywords: Outbox-pattern
permalink: /blog/outbox-pattern
usemathjax: true
---
# Out box pattern

# Hoàn cảnh ra đời

Trong mô hình Microservices, chúng ta thường publish một event cho các services cần thiết sau khi perform một action thay đổi trong database như thêm 1 record hoặc update status của 1 order. Nhưng modify database và publishing 1 event là 2 transactions hoàn toàn khác nhau. Làm sao chúng ta đảm bảo event được gửi đi sau khi thay đổi nội dung database, vì sau khi perform database change thì application của chúng ta hoàn toàn có thể sinh ra lỗi hoặc crash. Từ đó, sẽ xảy ra inconsistent data giữa các services và xảy ra các lỗi nghiêm trọng như thanh toán nhưng không nhận được đơn hàng, …

![Untitled](/assets/img/Out%20box%20pattern%20b63cf84af1cb4b44b76c2e07a803f592/Untitled.png)

# OutBox Pattern

Pattern này sẽ cung cấp 1 effective solution để event luôn được gửi đi đến các services khác nếu perform action trên database đã thành công.

Ideally, OutBox Pattern sẽ dùng 1 database transaction để wrap 2 actions chúng ta cần perform lại, bao gồm:
- Change data ở database

- Publish event

Nghĩa là chúng ta cần biến action publish event thành transaction của database (không phải database nào cũng hỗ trợ ACID nên chúng ta cần lưu ý), bằng cách chúng ta sẽ tạo ra 1 bảng mới tên là ******************MESSAGE******************, bất cứ khi nào perform action change ở database thì ta cũng write event cần publish vào bảng ****************MESSAGE**************** này. 

Sau đó, bằng 1 phương pháp khác mà ta sẽ gửi event này đến các services khác một cách tuần tự (eventually) bằng các phương pháp ví dụ như: [Polling publisher](https://microservices.io/patterns/data/polling-publisher.html), [Transaction log tailing](https://microservices.io/patterns/data/transaction-log-tailing.html), … Trong đó, pattern Transaction log tailing có thể được implement bằng 1 cách rất efficient cho các hệ thống lớn bằng cách sử dụng ****************************************Change Data Capture (CDC)**************************************** với [Debezium](https://debezium.io/) và [Kafka-Connect](https://docs.confluent.io/current/connect/index.html)

![Untitled](/assets/img/Out%20box%20pattern%20b63cf84af1cb4b44b76c2e07a803f592/Untitled%201.png)

# OutBox Pattern với Kafka Connect

## Thiết kế Solution

Giả sử chúng ta có một Student microservices giúp cho học sinh mới nhập học. Sau khi gia nhập, hệ thống cần gửi mail tới học sinh tất cả các khóa học mà sinh viên đó có thể tham gia. Ngoài ra, hệ thống Student được thiết kế theo [Event-Driven application](https://www.notion.so/Out-box-pattern-b63cf84af1cb4b44b76c2e07a803f592) học sinh gia nhập bằng cách sẽ được insert một record mới trong database và publish một event để thông báo trạng thái của sinh viên là hoàn thành. “Course Catalog” service sẽ lắng nghe Event này và perform những actions cần thiết.

Kafka-Connect framework sẽ run ở 1 service bên cạnh Kafka broker. Debezium connector cho database sẽ được deploy trên Kafka-Connect runtime, để capture sự thay đổi của database. Debezium connector sẽ theo dõi database transaction logs (write-ahead log) từ “Outbox” table và publish event đến topics đã được define trước.

![Untitled](/assets/img/Out%20box%20pattern%20b63cf84af1cb4b44b76c2e07a803f592/Untitled%202.png)

Solution này sẽ đảm bảo được [at-least-once-delivery](http://www.cloudcomputingpatterns.org/at_least_once_delivery/) vì Kafka Connect services đảm bảo rằng mỗi connector luôn luôn được chạy. Tuy nhiên, có một vấn đề là sẽ có tỉ lệ event sẽ được gửi đi nhiều hơn một lần khi connectors bị down và starting up trở lại. Để đảm bảo [exactly-once delivery](http://www.cloudcomputingpatterns.org/exactly_once_delivery/) consuming client phải [Idempotent](https://www.enterpriseintegrationpatterns.com/patterns/messaging/IdempotentReceiver.html), đảm bảo rằng duplicate event sẽ không được xử lý.

### Reference

[https://github.com/sohangp/outbox-pattern](https://github.com/sohangp/outbox-pattern)

[https://dzone.com/articles/implementing-the-outbox-pattern](https://dzone.com/articles/implementing-the-outbox-pattern)