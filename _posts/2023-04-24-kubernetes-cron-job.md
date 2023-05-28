---
layout: post
title:  "Kubernet cron kjob"
summary: "Kubernet cron kjob"
author: thanhtinhpas1
date: '2023-05-28 14:35:23 +0530'
category: k8s
thumbnail: 
keywords: Kubernet cron kjob
permalink: /blog/k8s-cron-job
usemathjax: true
---
# K8s CronJob

## Overview
Kubernetes Cronjob là một tính năng cho phép bạn lập kế hoạch chạy một jobs trong Kubernetes cluster ở một thời điểm nhất định.
CronJobs có thể được sử dụng cho nhiều nhiệm vụ khác nhau, ví dụ như backups dữ liệu, xử lý dữ liệu, những nhiệm vụ bảo trì định kỳ (periodic).

CronJobs sẽ được định nghĩa trong Kubernetes manifest file, cũng như các K8s resources khác. Bạn có thể định nghĩa kế hoạch của job, container image để run và bất ký biến, commands để truyền vào container. Khi một CronJob được tạo, K8s tự động tạo jobs dụa trên thời gian định sẵn.

Ví dụ một simple script được chạy mội phút

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
    name: my-cronjob
spec:
    schedule: "*/1 * * * *"
    jobTemplate:
        spec:
            template:
                spec:
                    containers:
                    - name: my-container
                      image: my-image:latest
                      command: ["/bin/sh"]
                      args: ["-c", "echo 'Hello, world!'"]
                    restartPolicy: OnFailure
```

Trong ví dụ trên, chúng ta tạo một CronJob có tên `my-cronjob` được chạy mỗi một phut bằng cách sử dụng cron syntax `*/1 * * * *`. CronJob được tạo sẽ run với conatain với image `my-image:latest` và run một command đơn giản `echo` để in ra dòng text `"Hello, world!"`. Restart policy (chính sách khởi động lại) được set sang `OnFailure`, có nghĩa là container sẽ được khởi động lại khi CronJob fail.

Another version more complex:
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cleanup-job
spec:
  schedule: "0 2 * * *"
  concurrencyPolicy: Allow
  suspend: false
  successfulJobsHistoryLimit: 10
  failedJobsHistoryLimit: 3
  startingDeadlineSeconds: 60
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cleanup-job
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/rm
            - -f
            - /tmp/*
          restartPolicy: OnFailure
```

CronJob trên sẽ chạy vào mỗi 2:00 AM để cleanup files trong folder `temp`. Ta có thể thấy CronJob sự thật là cũng giống như những resource khác trong K8s, có thể configure `deployments`, `replicate sets` và các kiểu `workloads` khác.
Sự khác nhau chính là:
- `schedule` (required): định nghĩa kế hoạch chạy của cronjob - schedule dựa trên [cron syntax](http://www.cronmaker.com/)
- `concurrencyPolicy` (optional): định nghĩa cách để handle concurrent jobs, default là `Allow`, nghĩa là nhiều job có thể run `simultaenously`
- `suspend` (optional): định nghĩa nếu các executions của 1 job có thể được bỏ qua, default là `false`
- `successfulJobsHistoryLimit` (optional): định nghĩa bao nhiêu lần executions thành công để track trong history, default là 3
- `failedJobsHistoryLimit` (optional): định nghĩa bao nhiêu lần executions failed để tracking trong lịch sử
- `startingDeadlineSeconds`: định nghĩa bao nhiêu giâu cho phép để start job trước khi cân nhắc là start job failed.

## Quản lý Cron Jobs
Chúng ta sẽ giỏa sử rằng put cronjob trong 1 file named `cronjob.yaml`. Chúng ta có thể tạo cronjob bằng `kubelet` command như sau:
```bash
kubelet create -f /path/to/cronjob.yaml
```

Ngoài ra, chúng ta có thể list ra cronjobs bằng command bên dưới:
```bash
kubectl get cronjob
NAME          SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cleanup-job   * 2 * * *   False     0        15s             32s
```

Chung ta cũng có thể xem chi tiết của cronjob, bao gồm lịch sử chạy:
```bash
kubectl describe cronjob hello
Name:                          cleanup-job
Namespace:                     default
Labels:                        <none>
Annotations:                   <none>
Schedule:                      * 2 * * *
Concurrency Policy:            Allow
Suspend:                       False
Successful Job History Limit:  3
Failed Job History Limit:      1
Starting Deadline Seconds:     <unset>
Selector:                      <unset>
Parallelism:                   <unset>
Completions:                   <unset>
Pod Template:
  Labels:  <none>
  Containers:
   hello:
    Image:      busybox:1.28
    Port:       <none>
    Host Port:  <none>
    Command:
      /bin/rm
      -f
      /tmp/*
    Environment:     <none>
    Mounts:          <none>
  Volumes:           <none>
Last Schedule Time:  Mon, 30 May 2022 02:00:00 -0600
Active Jobs:         <none>
Events:
  Type    Reason            Age                   From                Message
  ----    ------            ----                  ----                -------
  Normal  SuccessfulCreate  16m                   cronjob-controller  Created job cleanup-job-27565242
  Normal  SawCompletedJob   16m                   cronjob-controller  Saw completed job: cleanup-job-27565242, status: Complete
```

Cuối cùng, nếu không cần cronjob nữa, chúng ta có thể `delete` bằng cách:
```bash
kubectl delete cronjob cleanup-job
```

## Lợi ích khi sử dụng cronjob
Kubernetes (k8s) cronjob và cronjob trực tiếp trên application đều được sử dụng để lập lịch thực thi các tác vụ định kỳ trên hệ thống của bạn. Tuy nhiên, có một số lợi ích của việc sử dụng k8s cronjob so với cronjob trực tiếp trên application:

Quản lý tài nguyên tốt hơn: Khi sử dụng k8s cronjob, bạn có thể quản lý tài nguyên một cách tốt hơn. Kubernetes sẽ tự động điều chỉnh số lượng bản sao của các tác vụ để đảm bảo rằng chúng không tốn quá nhiều tài nguyên trên hệ thống.

Dễ dàng mở rộng: Với k8s cronjob, bạn có thể dễ dàng mở rộng hệ thống của mình bằng cách thêm các worker node vào cluster. Khi bạn thêm các node mới, Kubernetes sẽ tự động triển khai các tác vụ cronjob trên những node này để tối ưu hóa hiệu suất.

Độ tin cậy cao hơn: K8s cronjob giúp đảm bảo rằng các tác vụ sẽ được thực thi đúng thời điểm và đúng số lần được định trước. Nếu một node trong cluster gặp sự cố, Kubernetes sẽ tự động di chuyển tác vụ sang một node khác để đảm bảo rằng chúng được thực thi đúng thời điểm.

Dễ dàng quản lý: Khi sử dụng k8s cronjob, bạn có thể dễ dàng quản lý các tác vụ cronjob trên nhiều node khác nhau trong cluster. Bạn có thể thực hiện các hoạt động như triển khai, cập nhật và xoá các tác vụ với một vài lệnh đơn giản.

Tuy nhiên, sử dụng cronjob trực tiếp trên application cũng có một số lợi ích. Nếu ứng dụng của bạn đã được triển khai trên một máy chủ đơn lẻ và không cần sử dụng Kubernetes, thì việc sử dụng cronjob trực tiếp trên application sẽ đơn giản hơn và dễ dàng hơn để triển khai.
