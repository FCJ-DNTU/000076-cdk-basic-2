+++
title = "Dọn dẹp tài nguyên"
date = 2024
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

#### Dọn dẹp tài nguyên
Tương tự như phần CDK cơ bản, bạn có thể dọn dẹp hết các tài nguyên đã tạo để tránh bị charge phí không đáng có. Để xoá các tài nguyên trong stack, chạy lệnh.

```
cdk destroy
```

Hoặc có thể dọn dẹp thủ công

![delete-manual](/images/6-clean-up-resources/6.1-delete-manual.png)

Nếu như bạn tải các tệp vào trong bucket, thì bucket sẽ không thể bị hủy bởi lệnh destroy của cdk. Vì thế mà bạn sẽ cần phải xóa thủ công tất cả các tệp tin ở bên trong bucket và sau đó là xóa bucket đó đi.

Chúng mừng bạn vì đã hoàn thành bài thực hành CDK cơ bản 2