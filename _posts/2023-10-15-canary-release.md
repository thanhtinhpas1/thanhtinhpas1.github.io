---
layout: post
title:  "Canary Release"
summary: "Canary Relase"
author: thanhtinhpas1
category: microservice
keywords: Release, Canary
thumbnail: https://global-uploads.webflow.com/5c9200c49b1194323aff7304/63074d2b8bb03f0514540ae4_Canary_Diagram-1.png
permalink: /blog/canary-release
usemathjax: true
---

# Canary Release

Canary release là 1 kỹ thuật để giảm thiểu rủi ro khi release 1 version mới của sản phẩm trên môi trường Production bằng cách rolling out version mới cho từng tập nhỏ users trước khi cho toàn bộ.

Tương tự như [BlueGreenDeployment](https://martinfowler.com/bliki/BlueGreenDeployment.html) bắt đầu deploy version mới cho từng tập hạ tầng thay vì users.

<!-- ![Untitled](/assets/img/canary/Untitled.png) -->
<img src="/assets/img/canary/Untitled.png" width="40%">

Khi chúng ta kiểm chứng rằng version mới đều ổn thì khi đó chúng ta routing 1 vài users để thử nghiệm. Một vài công ty có thể rolling out cho nội bộ nhân viên sử dụng trước khi đưa ra thị trường, 1 cách khác nữa là lựa chọn users theo những tiêu chí riêng dựa vào dữ liệu người dùng

![Untitled](/assets/img/canary/Untitled%201.png)

Nếu chung ta tự tin hơn với version mới này thì sẽ rolling out càng lúc càng nhiều users hơn. Ngoài ra, có 1 good practice để rollout new version là tái sử dụng hạ tầng bằng cách sử dụng [**PhoenixServer**](https://martinfowler.com/bliki/PhoenixServer.html) hoặc dự phòng hạ tầng mới và hủy bỏ hạ tầng cũ bằng cách sử dụng **[ImmutableServer](https://martinfowler.com/bliki/ImmutableServer.html)**

Canary release là một ứng dụng của [**ParallelChange**](https://martinfowler.com/bliki/ParallelChange.html), khi mà quá trình chuyển đổi xảy ra đến những users cuối cùng được tiếp cận đến version mới. Ở thời điểm đó, chúng ta có thể hủy bỏ hạ tầng cũ. Nếu có bất kỳ vấn đề nào với version mới, chúng ta có thể route lại users quay lại version cũ cho đến khi vấn đề được xử lý.

![Untitled](/assets/img/canary/Untitled%202.png)

Một lợi ích của canary releases là khả năng testing của version mới trên môi trường production một cách an toàn hơn. Sử dụng canary release chúng ta sẽ có thời gian để capture sự ảnh hưởng của version mới đến môi trường Production.

Trong ngữ cảnh môi trường phân tán, thay vì sử dụng router để quyết định users nào sẽ được route sang version mới, có 1 cách phổ biến hơn là sử dụng partioning khác nhau. Ví dụ, khi chúng ta có users ở nhiều khu vực địa lý khác nhau, thì có thể rollout theo từng khu vực, Facebook là 1 trong những big tech sử dụng chiến lược này. Ngoài ra Facebook sẽ rollout cho nội bộ nhân viên trước và có tất cả [**FeatureFlags**](https://www.notion.so/Canary-Release-1b8222922247432aba9f15ff7cb6d273?pvs=21) bật vì vậy FB sẽ dễ dàng nhận ra được những vấn đề khi nó xảy ra.

![Untitled](/assets/img/canary/Untitled%203.png)

Canary release có thể sử dụng để thực hiện A/B testing vì có những điểm tương đồng so với A/B testing thông thường. Tuy nhiên, chúng ta không nên nhập nhằng giữa Canary Release và A/B testing vì Canary release là 1 cách nhận biết vấn đề và regression tests new version trên môi trường PROD, trong khi đó A/B testing dùng để thử nghiệm 1 biến thể mới để xem nó có thực sự hiệu quả hay không.

Vẫn có 1 bất lợi của CanaryRelase là chúng ta phải quản lý nhiều version của sản phẩm trong cùng 1 lúc. Chúng ta có thể nhiều versions nhưng nên giữ số lượng versions ít nhất có thể để giảm sự phức tạp và resource cho quá trình release.

Ngoài ra CanaryRelease đòi hỏi Backend và Database phải hỗ trợ nhiều version trong suốt quá trình rollout.