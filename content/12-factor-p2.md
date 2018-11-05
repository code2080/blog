---
title: "Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 2/2"
date: 2018-09-25T00:00:00+07:00
draft: false
author: "cstd"
tags: ["software development", "translation"]
---

* [Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 1 / 2](/12-factor-p1)<br/>
* Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 2 / 2 (Đang xem)

(Tiếp tục)

# 6. Processes (Tiến trình)

**Execute the app as one or more stateless processes**<br/>
**Thực thi ứng dụng như một hoặc nhiều tiến trình phi trạng thái**

<dl>
<small style="color: gray">
The app is executed in the execution environment as one or more processes.
</small>
</dl>

Ứng dụng được thực thi trong môi trường thực thi bởi một hoặc nhiều tiến trình.

<dl>
<small style="color: gray">
In the simplest case, the code is a stand-alone script, the execution environment is a developer’s local laptop with an installed language runtime, and the process is launched via the command line (for example, python my_script.py). On the other end of the spectrum, a production deploy of a sophisticated app may use many process types, instantiated into zero or more running processes.
</small>
</dl>

Trong trường hợp đơn giản, code là một đoạn mã độc lập, môi trường thực thi là một máy tính cá nhân của nhà phát triển với trình chạy ngôn ngữ đã được cài đặt, và tiến trình được khởi chạy thông qua dòng lệnh (ví dụ `python my_script.py`). Ở trường hợp phức tạp hơn, một bản triển khai ở production của một ứng dụng phức tạp có thể sử dụng [nhiều kiểu tiến trình, được khởi tạo từ 0 đến nhiều tiến trình được chạy](#8-concurrency-đồng-thời).

<dl>
<small style="color: gray">
Twelve-factor processes are stateless and share-nothing. Any data that needs to persist must be stored in a stateful backing service, typically a database.
</small>
</dl>

**Các tiến trình theo twelve-factor thì không có trạng thái và [không chia sẻ](http://en.wikipedia.org/wiki/Shared_nothing_architecture) bất cứ thứ gì.** Bất kỳ dữ liệu cần được lưu vĩnh viễn phải được lưu ở một backing service, thông thường là cơ sở dữ liệu.

<dl>
<small style="color: gray">
The memory space or filesystem of the process can be used as a brief, single-transaction cache. For example, downloading a large file, operating on it, and storing the results of the operation in the database. The twelve-factor app never assumes that anything cached in memory or on disk will be available on a future request or job – with many processes of each type running, chances are high that a future request will be served by a different process. Even when running only one process, a restart (triggered by code deploy, config change, or the execution environment relocating the process to a different physical location) will usually wipe out all local (e.g., memory and filesystem) state.
</small>
</dl>

Không gian nhớ hoặc tệp hệ thống của tiến trình có thể được sử dụng như một bộ nhớ đệm ngắn gọn, đơn lẻ. Ví dụ, khi tải về một tệp lớn, thao trên trên tệp đấy, và lưu trữ kết quả thao tác vào cơ sở dữ liệu. Ứng dụng twelve-factor không bao giờ giả định rằng bất cứ thứ gì được ghi nhớ đệm trong bộ nhớ hoặc ổ đĩa sẽ sẵn sàng được dùng bởi một request (yêu cầu) hoặc một job (việc) trong tương lai - với nhiều tiến trình của mỗi loại đang chạy, khả năng cao là một request tương lai sẽ được phục vụ bởi một tiến trình khác. Thậm chí khi chỉ có một tiến trình đang chạy, việc khởi động lại (được gọi khi triển khai code, thay đổi cấu hình, hoặc môi trường thực thi thay đổi vị trí của tiến trình đến vị trí vật lý khác) cũng sẽ thường xoá toàn bộ trạng thái cục bộ (ví dụ bộ nhớ và tệp hệ thống).

<dl>
<small style="color: gray">
Asset packagers like django-assetpackager use the filesystem as a cache for compiled assets. A twelve-factor app prefers to do this compiling during the build stage. Asset packagers such as Jammit and the Rails asset pipeline can be configured to package assets during the build stage.
</small>
</dl>

Asset packagers (Trình đóng gói tài sản) như django-assetpackager sử dụng filesystem (tệp hệ thống) như một bộ nhớ đệm cho biên dịch asset (tài sản). Một ứng dụng twelve-factor sẽ thường thích làm việc biên dịch này trong giai đoạn đóng gói. Asset packagers ví dụ Jammit và Rails asset pipeline có thể được cấu hình để đóng gói asset trong giai đoạn đóng gói.

<dl>
<small style="color: gray">
Some web systems rely on “sticky sessions” – that is, caching user session data in memory of the app’s process and expecting future requests from the same visitor to be routed to the same process. Sticky sessions are a violation of twelve-factor and should never be used or relied upon. Session state data is a good candidate for a datastore that offers time-expiration, such as Memcached or Redis.
</small>
</dl>

Một số hệ thống web dựa trên "sticky sessions" - mà nó sẽ lưu dữ liệu phiên làm việc của người dùng vào bộ nhớ đệm của tiến trình ứng dụng và hi vọng các request tương lai đến từ những người dùng giống nhau sẽ được định tuyến vào cùng tiến trình. Sticky sessions đã vi phạm phương pháp twelve-factor và không bao giờ nên sử dụng hay dựa vào. Session state data (Dữ liệu trạng thái phiên) là đề cử tốt cho một datastore (vùng lưu dữ liệu), nó đưa ra giới hạn thời gian, ví dụ Memcached hay Redis.

# 7. Port binding

**Export services via port binding**<br/>
**Xuất các dịch vụ thông qua port binding (gắn vào cổng)**

<dl>
<small style="color: gray">
Web apps are sometimes executed inside a webserver container. For example, PHP apps might run as a module inside Apache HTTPD, or Java apps might run inside Tomcat.
</small>
</dl>

Các ứng dụng web thỉnh thoảng đc thực thi bên trong một máy chủ web. Ví dụ, ứng dụng PHP có thể được chạy như một mô-đun bên trong Apache HTTPD, hay ứng dụng Java có thể được chạy bên trong Tomcat.

<dl>
<small style="color: gray">
The twelve-factor app is completely self-contained and does not rely on runtime injection of a webserver into the execution environment to create a web-facing service. The web app exports HTTP as a service by binding to a port, and listening to requests coming in on that port.
</small>
</dl>

**Ứng dụng twelve-factor thì hoàn toàn tự chứa** và không dựa vào runtime injection của một máy chủ web vào trong môi trường thực thi để tạo một dịch vụ giao tiếp web. Ứng dụng web **xuất giao thức HTTP như một dịch vụ bằng cách binding đến một cổng**, và lắng nghe request đến từ cổng đó.

<dl>
<small style="color: gray">
In a local development environment, the developer visits a service URL like http://localhost:5000/ to access the service exported by their app. In deployment, a routing layer handles routing requests from a public-facing hostname to the port-bound web processes.
</small>
</dl>

Trong môi trường phát triển cục bộ, nhà phát triển truy cập một địa chỉ URL của dịch vụ ví dụ `http://localhost:5000/` để truy cập vào dịch vụ được xuất bởi ứng dụng của chúng. Trong môi trường phát triển, một lớp định tuyến xử lý những request định tuyến từ một hostname giao tiếp công khai đến những tiến trình web có cổng.

<dl>
<small style="color: gray">
This is typically implemented by using dependency declaration to add a webserver library to the app, such as Tornado for Python, Thin for Ruby, or Jetty for Java and other JVM-based languages. This happens entirely in user space, that is, within the app’s code. The contract with the execution environment is binding to a port to serve requests.
</small>
</dl>

Thông thường ứng dụng được thực thi triển khai bằng việc sử dụng khai báo phụ thuộc để thêm thư viện máy chủ web vào ứng dụng, ví dụ như Tornado cho Python, Thin cho Ruby, hay Jetty cho Java và các ngôn ngữ trên nền JVM khác. Việc này xảy ra toàn bộ trong *không gian người dùng*, đó chính là trong code của ứng dụng. Thoả thuận môi trường thực thi đang binding đến một cổng là phục vụ các request đến.

<dl>
<small style="color: gray">
HTTP is not the only service that can be exported by port binding. Nearly any kind of server software can be run via a process binding to a port and awaiting incoming requests. Examples include ejabberd (speaking XMPP), and Redis (speaking the Redis protocol).
</small>
</dl>

HTTP không phải là dịch vụ duy nhất có thể xuất bởi port binding. Hầu như bất kì kiểu phần mềm máy chủ nào đều có thể chạy thông qua tiến trình binding đến một cổng và chờ đợi request đến. Ví dụ bao gồm ejabberd (giao tiếp XMPP), và Redis (giao tiếp Redis protocol).

<dl>
<small style="color: gray">
Note also that the port-binding approach means that one app can become the backing service for another app, by providing the URL to the backing app as a resource handle in the config for the consuming app.
</small>
</dl>

Cũng lưu ý rằng cách tiếp cận port-binding nghĩa là một ứng dụng có thể trở thành backing service cho một ứng dụng khác, bằng việc cung cấp địa chỉ URL đến ứng dụng phía sau như là một tài nguyên xử lý trong cấu hình cho ứng dụng cần dùng.

# 8. Concurrency (Đồng thời)

**Scale out via the process model**<br/>
**Mở rộng thông qua mô hình tiến trình**

<dl>
<small style="color: gray">
Any computer program, once run, is represented by one or more processes. Web apps have taken a variety of process-execution forms. For example, PHP processes run as child processes of Apache, started on demand as needed by request volume. Java processes take the opposite approach, with the JVM providing one massive uberprocess that reserves a large block of system resources (CPU and memory) on startup, with concurrency managed internally via threads. In both cases, the running process(es) are only minimally visible to the developers of the app.
</small>
</dl>

Bất kỳ chương trình máy tính nào, khi chạy, đều được biểu diễn bởi một hoặc nhiều tiến trình. Ứng dụng web thì có đủ kiểu biểu mẫu thực thi tiến trình khác nhau. Ví dụ, các tiến trình PHP chạy như một tiến trình con của Apache, được chạy theo yêu cầu cần bởi lượng request. Các tiến trình Java thì tiếp cận ngược lại, với JVM cung cấp một uberprocess lớn, nó dự trữ một khối lớn của tài nguyên hệ thống khi khởi chạy, với concurrency được quản lý nội bộ qua các luồng. Trong cả hai trường hợp, các tiến trình đang chạy chỉ hiển thị tối thiểu với các nhà phát triển ứng dụng.

*Scale is expressed as running processes, workload diversity is expressed as process types:*<br/>
*Sự mở rộng được thể hiện như các tiến trình đang chạy, sự đa dạng của workload (tải công việc) thì được thể hiện như các kiểu của tiến trình:*
![alt text](/images/process-types.png "Scale is expressed as running processes, workload diversity is expressed as process types")

<dl>
<small style="color: gray">
In the twelve-factor app, processes are a first class citizen. Processes in the twelve-factor app take strong cues from the unix process model for running service daemons. Using this model, the developer can architect their app to handle diverse workloads by assigning each type of work to a process type. For example, HTTP requests may be handled by a web process, and long-running background tasks handled by a worker process.
</small>
</dl>

Trong ứng dụng twelve-factor, các tiến trình được coi là các công dân hạng nhất. Các tiến trình trong ứng dụng twelve-factor lấy các gợi ý mạnh mẽ từ [mô hình tiến trình của unix cho các dịch vụ daemon đang chạy](/applying-the-unix-process-model-to-web-apps/). Sử dụng mô hình này, nhà phát triển có thể kiến trúc ứng dụng của họ để xử lý các workload đa dạng bằng cách gán mỗi kiểu công việc cho một kiểu tiến trình. Ví dụ, các HTTP request có thể được xử lý bởi các tiến trình web, và các task (nhiệm vụ) chạy nền thời gian dài được xử lý bởi một tiến trình worker.

<dl>
<small style="color: gray">
This does not exclude individual processes from handling their own internal multiplexing, via threads inside the runtime VM, or the async/evented model found in tools such as EventMachine, Twisted, or Node.js. But an individual VM can only grow so large (vertical scale), so the application must also be able to span multiple processes running on multiple physical machines.
</small>
</dl>

Điều này không loại trừ những tiến trình riêng rẽ xử lý công việc nội bộ cồng kềnh của chúng, thông qua các luồng bên trong runtime của máy ảo, hoặc mô hình bất đồng bộ/hướng sự kiện được tìm thấy trong các công cụ ví dụ như EventMachine, Twisted, hay Node.js. Nhưng một máy ảo riêng rẽ chỉ có thể phát triển lớn lên (theo chiều dọc), nên ứng dụng cũng phải có khả năng mở rộng nhiều tiến trình chạy trên nhiều máy tính vật lý.

<dl>
<small style="color: gray">
The process model truly shines when it comes time to scale out. The share-nothing, horizontally partitionable nature of twelve-factor app processes means that adding more concurrency is a simple and reliable operation. The array of process types and number of processes of each type is known as the process formation.
</small>
</dl>

Mô hình tiến trình chỉ thực sự toả sáng khi nói đến thời gian để mở rộng quy mô. [Không chia sẻ, tính chất phân chia theo chiều ngang của tiến trình của ứng dụng twelve-factor](#6-processes-tiến-trình) nghĩa là việc thêm concurrency là một thao tác đơn giản và đáng tin cậy. Một mảng các kiểu tiến trình và số lượng tiến trình theo từng kiểu được gọi là *process formation*.

<dl>
<small style="color: gray">
Twelve-factor app processes should never daemonize or write PID files. Instead, rely on the operating system’s process manager (such as systemd, a distributed process manager on a cloud platform, or a tool like Foreman in development) to manage output streams, respond to crashed processes, and handle user-initiated restarts and shutdowns.
</small>
</dl>

Những tiến trình của ứng dụng twelve-factor [không bao giờ nên daemonize](http://dustin.github.com/2010/02/28/running-processes.html) hoặc viết vào tệp PID. Thay vì đó, chỉ dựa trên trình quản lý tiến trình của hệ điều hành (ví dụ systemd, một trình quản lý tiến trình phân tán trên nền tảng cloud, hoặc một công cụ như Foreman trong quá trình phát triển) để quản lý [các dòng dữ liệu đầu ra](#11-logs-các-ghi-chép), phản hồi lại các tiến trình bị hỏng, và xử lý các hành động tắt và khởi động lại do người dùng khởi tạo.

**Bổ sung** thêm từ: https://www.oreilly.com/ideas/concurrency-in-modern-apps

There was a time when, if an application reached the limit of its capacity, the solution was to increase its size. If an application could only handle some number of requests per minute, then the preferred solution was to simply make the application bigger.

Adding CPUs, RAM, and other resources (virtual or physical) to a single monolithic application is called vertical scaling, and this type of behavior is typically frowned upon in civilized society these days.

A much more modern approach, one ideal for the kind of elastic scalability that the cloud supports, is to scale out, or horizontally. Rather than making a single big process even larger, you create multiple processes, and then distribute the load of your application among those processes.

Most cloud providers have perfected this capability to the point where you can even configure rules that will dynamically scale the number of instances of your application based on load or other run‐time telemetry available in a system.

If you are building disposable, stateless, share-nothing processes then you will be well positioned to take full advantage of horizontal scaling and running multiple, concurrent instances of your application so that it can truly thrive in the cloud.

# 9. Disposability (Tính sẵn sàng)

**Maximize robustness with fast startup and graceful shutdown**<br/>
**Tối đa sự mạnh mẽ với khởi động nhanh và tắt nhã nhặn**

<dl>
<small style="color: gray">
The twelve-factor app’s processes are disposable, meaning they can be started or stopped at a moment’s notice. This facilitates fast elastic scaling, rapid deployment of code or config changes, and robustness of production deploys.
</small>
</dl>

[Tiến trình](#6-processes-tiến-trình) của ứng dụng twelve-factor thì có tính sẵn sàng, nghĩa là chúng có thể bật hoặc tắt khi có thông báo tại một thời điểm nào đấy. Điều này làm dễ dàng cho việc mở rộng co giãn nhanh, triển khai nhanh chóng khi thay đổi code hoặc cấu hình, và sự chắc chắn của các bản triển khai của production.

<dl>
<small style="color: gray">
Processes should strive to minimize startup time. Ideally, a process takes a few seconds from the time the launch command is executed until the process is up and ready to receive requests or jobs. Short startup time provides more agility for the release process and scaling up; and it aids robustness, because the process manager can more easily move processes to new physical machines when warranted.
</small>
</dl>

Các tiến trình nên cố gắng giảm tối đa thời gian khởi động. Lý tưởng là, một tiến trình mất vài giây từ khi chạy câu lệnh khởi động đến khi tiến trình chạy và sẵn sàng nhận request và job. Thời gian khởi động ngắn cung cấp sự nhanh nhẹn hơn cho tiến trình phát hành và mở rộng; và nó tăng cường sự mạnh mẽ, bởi vì trình quản lý tiến trình có thể dễ dàng hơn trong việc di chuyển các tiến trình đến máy tính vật lý mới khi được bảo hành.

<dl>
<small style="color: gray">
Processes shut down gracefully when they receive a SIGTERM signal from the process manager. For a web process, graceful shutdown is achieved by ceasing to listen on the service port (thereby refusing any new requests), allowing any current requests to finish, and then exiting. Implicit in this model is that HTTP requests are short (no more than a few seconds), or in the case of long polling, the client should seamlessly attempt to reconnect when the connection is lost.
</small>
</dl>

Các tiến trình tắt nhã nhặn khi chúng nhận một tín hiện [SIGTERM](http://en.wikipedia.org/wiki/SIGTERM) từ trình quản lý tiến trình. Với một tiến trình web, tắt nhã nhặn đạt được bởi việc ngừng lắng nghe trên các cổng dịch vụ (bằng cách ấy từ chối bất kỳ các request mới nào), cho phép bất kì request hiện tại nào kết thúc, và sau đó thoát. Ngụ ý trong mô hình này là HTTP request thì ngắn (không quá vài giây), hoặc trong trường hợp long-polling, máy khách sẽ cố gắng kết nối lại khi mất kết nối.

<dl>
<small style="color: gray">
For a worker process, graceful shutdown is achieved by returning the current job to the work queue. For example, on RabbitMQ the worker can send a NACK; on Beanstalkd, the job is returned to the queue automatically whenever a worker disconnects. Lock-based systems such as Delayed Job need to be sure to release their lock on the job record. Implicit in this model is that all jobs are reentrant, which typically is achieved by wrapping the results in a transaction, or making the operation idempotent.
</small>
</dl>

Với một tiến trình worker, tắt nhã nhặn đạt được bằng việc trả lại job hiện tại vào hàng đợi. Ví dụ, trên RabbitMQ worker có thể gửi một NACK; trên Beanstalkd, job được trả lại hàng đợi tự động khi một worker bị mất kết nối. Hệ thống Lock-based ví dụ Delayed Job cần chắc chắn nhả lock của chúng trên bản ghi job. Ngụ ý trong mô hình này là tất cả các job có thể [dùng lại được](http://en.wikipedia.org/wiki/Reentrant_%28subroutine%29), mà thông thường đạt được được bởi bao kết quả trong một transaction (giao dịch), hoặc làm cho thao tác [không thay đổi giá trị](http://en.wikipedia.org/wiki/Idempotence).

<dl>
<small style="color: gray">
Processes should also be robust against sudden death, in the case of a failure in the underlying hardware. While this is a much less common occurrence than a graceful shutdown with SIGTERM, it can still happen. A recommended approach is use of a robust queueing backend, such as Beanstalkd, that returns jobs to the queue when clients disconnect or time out. Either way, a twelve-factor app is architected to handle unexpected, non-graceful terminations. Crash-only design takes this concept to its logical conclusion.
</small>
</dl>

Các tiến trình cũng phải mạnh mẽ để chống lại việc chết đột ngột, trong trường hợp một hỏng hóc của phần cứng bên dưới. Tuy điều này hiếm khi xảy ra hơn so với việc tắt nhã nhặn với SIGTERM, nhưng nó vẫn có thể xảy ra. Một cách tiếp cận khuyến khích là sử dụng một backend hàng đợi mạnh mẽ, ví dụ Beanstalkd, mà có thể gửi trả job vào hàng đợi khi máy khách mất kết nối hoặc quá thời gian. Dù bằng cách nào, một ứng dụng twelve-factor cũng phải được kiến trúc để xử lý việc ngắt kết nối không mong muốn, không nhã nhặn. [Thiết kế crash-only](http://lwn.net/Articles/191059/) lấy khái niệm này cho [kết luận logic](http://docs.couchdb.org/en/latest/intro/overview.html) của nó.

# 10. Dev/prod parity (Tương đồng giữa phát triển và sản phẩm)

**Keep development, staging, and production as similar as possible**<br/>
**Giữ môi trường development, staging, and production giống nhau nhất có thể**

<dl>
<small style="color: gray">
Historically, there have been substantial gaps between development (a developer making live edits to a local deploy of the app) and production (a running deploy of the app accessed by end users). These gaps manifest in three areas:
</small>
</dl>

Trong lịch sử, đã có những lỗ hổng thực tế giữa development (một nhà phát triển thực hiện những chỉnh sửa trực tiếp lên bản triển khai cục bộ của ứng dụng) và production (một bản triển khai đang chạy của ứng dụng được truy cập bởi người dùng cuối). Những lỗ hổng này biểu thị trong ba vùng:

<dl>
<small style="color: gray">
- The time gap: A developer may work on code that takes days, weeks, or even months to go into production.<br/>
- The personnel gap: Developers write code, ops engineers deploy it.<br/>
- The tools gap: Developers may be using a stack like Nginx, SQLite, and OS X, while the production deploy uses Apache, MySQL, and Linux.
</small>
</dl>

* **Lỗ hổng thời gian**: Một nhà phát triển có thể làm việc trên code mất nhiều ngày, nhiều tuần, thậm chí nhiều tháng trước khi đưa lên production.
* **Lỗ hổng con người**: Các nhà phát triển viết code còn kỹ sư hệ thống triển khai nó.
* **Lỗ hổng công cụ**: Các nhà phát triển có thể sử dụng một stack (nhóm) như Nginx, SQLite, và OS X, trong khi bản triển khai trên production sử dụng Apache, MySQL, và Linux.

<dl>
<small style="color: gray">
The twelve-factor app is designed for continuous deployment by keeping the gap between development and production small. Looking at the three gaps described above:
</small>
</dl>

**Ứng dụng twelve-factor được thiết kế cho [triển khai liên tục](http://avc.com/2011/02/continuous-deployment/) bằng việc giữ lỗ hổng giữa development và production nhỏ**. Nhìn vào ba lỗ hổng mô tả ở trên:

<dl>
<small style="color: gray">
- Make the time gap small: a developer may write code and have it deployed hours or even just minutes later.<br/>
- Make the personnel gap small: developers who wrote code are closely involved in deploying it and watching its behavior in production.<br/>
- Make the tools gap small: keep development and production as similar as possible.
</small>
</dl>

* Làm cho lỗ hổng thời gian nhỏ: một nhà phát triển có thể viết code và để nó được triển khai trong vài giờ, thậm chí chỉ vài phút sau đó.
* Làm cho chỗ hổng con người nhỏ: những nhà phát triển người viết code liên quan chặt chẽ đến việc triển khai code và theo dõi hành vi của nó trong môi trường production. 
* Làm cho lỗ hổng các công cụ nhỏ: giữ môi trường development và production giống nhau nhất có thể.

Tóm tắt những thứ trên vào một bảng như sau:

||Ứng dụng truyền thống|Ứng dụng twelve-factor|
|---|---|---|
|**Thời gian giữa các deploy**|Vài tuần|Vài giờ|
|**Người viết code và người triển khai code**|Người khác nhau|Cùng người|
|**Môi trường dev với production**|Khác nhau|Giống nhau nhất có thể|

<dl>
<small style="color: gray">
Backing services, such as the app’s database, queueing system, or cache, is one area where dev/prod parity is important. Many languages offer libraries which simplify access to the backing service, including adapters to different types of services. Some examples are in the table below.
</small>
</dl>

Các backing service, ví dụ cơ sở dữ liệu của ứng dụng, hệ thống hàng đợi, hoặc bộ đệm, là một nơi mà sự tương đồng của dev/prod là quan trọng. Nhiều ngôn ngữ đưa ra các thư viện giúp đơn giản hoá truy cập tới backing service, gồm *các adapter (bộ chuyển đổi)* đến các loại dịch vụ khác nhau. Một số ví dụ trong bảng sau:

|Loại|Ngôn ngữ|Thư viện|Adapters|
|---|---|---|---|
|Database|Ruby/Rails|ActiveRecord|MySQL, PostgreSQL, SQLite|
|Queue|Python/Django|Celery|RabbitMQ, Beanstalkd, Redis|
|Cache|Ruby/Rails|ActiveSupport::Cache|Memory, filesystem, Memcached|

<dl>
<small style="color: gray">
Developers sometimes find great appeal in using a lightweight backing service in their local environments, while a more serious and robust backing service will be used in production. For example, using SQLite locally and PostgreSQL in production; or local process memory for caching in development and Memcached in production.
</small>
</dl>

Nhà phát triển thỉnh thoảng tìm kiếm kêu gọi tuyệt vời sử dụng một backing service nhỏ nhẹ trong môi trường cục bộ của họ, trong khi một backing service mạnh mẽ và nghiêm túc hơn sẽ sử dụng trong môi trường production. Ví dụ, sử dụng SQLite ở cục bộ và sử dụng PostgreSQL ở production; hoặc sử dụng bộ nhớ tiến trình ở cục bộ cho lưu đệm ở môi trường development và Memcached trong môi trường production.

<dl>
<small style="color: gray">
The twelve-factor developer resists the urge to use different backing services between development and production, even when adapters theoretically abstract away any differences in backing services. Differences between backing services mean that tiny incompatibilities crop up, causing code that worked and passed tests in development or staging to fail in production. These types of errors create friction that disincentivizes continuous deployment. The cost of this friction and the subsequent dampening of continuous deployment is extremely high when considered in aggregate over the lifetime of an application.
</small>
</dl>

**Nhà phát triển twelve-factor thì tránh sử dụng các backing service khác nhau giữa development và production**, thậm chí cả khi apdapters theo lý thuyết sẽ trừu tượng hoá bất kì sự khác nhau nào trong các backing service. Sự khác nhau giữa các backing service nghĩa là một phần nhỏ sự không tương thích sẽ làm việc và pass (vượt qua) các test (kiểm tra) trên development hoặc staging nhưng thất bại trên production. Các kiểu lỗi này tạo ra sự ma sát gây trở ngại trong triển khai liên tục. Giá của sự ma sát và sự cản trở đến sau này của triển khai liên tục là rất cao khi xem xét trong tích luỹ dần dần qua lifetime (thời gian sống) của ứng dụng.

<dl>
<small style="color: gray">
Lightweight local services are less compelling than they once were. Modern backing services such as Memcached, PostgreSQL, and RabbitMQ are not difficult to install and run thanks to modern packaging systems, such as Homebrew and apt-get. Alternatively, declarative provisioning tools such as Chef and Puppet combined with light-weight virtual environments such as Docker and Vagrant allow developers to run local environments which closely approximate production environments. The cost of installing and using these systems is low compared to the benefit of dev/prod parity and continuous deployment.
</small>
</dl>

Những dịch vụ cục bộ nhỏ nhẹ thì kém hấp dẫn so với trước đây. Các backing service hiện đại ví dụ Memcached, PostgreSQL, và RabbitMQ không khó để cài đặt và chạy nhờ các hệ thống đóng gói hiện tại, ví dụ Homebrew hay apt-get. Thêm nữa, các công cụ cung cấp khai báo ví dụ Chef and Puppet đã kết hợp với những môi trường ảo nhỏ nhẹ ví dụ như Docker và Vagrant cho phép các nhà phát triển chạy môi trường cục bộ gần đúng với môi trường production. Giá của việc cài đặt và sử dụng những hệ thống này là thấp hơn khi so với lợi ích về sự tương đồng dev/prod và triển khai liên tục.

<dl>
<small style="color: gray">
Adapters to different backing services are still useful, because they make porting to new backing services relatively painless. But all deploys of the app (developer environments, staging, production) should be using the same type and version of each of the backing services.
</small>
</dl>

Adapters cho các backing service khác nhau vẫn hữu ích, bởi vì chúng giúp chuyển sang các backing service mới tương đối không vất vả. Nhưng tất cả các bản triển khai của ứng dụng (môi trường của developer, staging, production) nên sử dụng các kiểu và phiên bản giống nhau của các backing service.

# 11. Logs (Các ghi chép)

**Treat logs as event streams**<br/>
**Coi các log như dòng dữ liệu sự kiện**

<dl>
<small style="color: gray">
Logs provide visibility into the behavior of a running app. In server-based environments they are commonly written to a file on disk (a “logfile”); but this is only an output format.
</small>
</dl>

*Các log* cung cấp khả năng quan sát đến hành vi của ứng dụng đang chạy. Trong những môi trường trên nền máy chủ, chúng thông thường ghi và một tệp trên ổ đĩa (một logfile); nhưng đây chỉ là một định dạng đầu ra.

<dl>
<small style="color: gray">
Logs are the stream of aggregated, time-ordered events collected from the output streams of all running processes and backing services. Logs in their raw form are typically a text format with one event per line (though backtraces from exceptions may span multiple lines). Logs have no fixed beginning or end, but flow continuously as long as the app is operating.
</small>
</dl>

Các log là [dòng dữ liệu](https://adam.herokuapp.com/past/2011/4/1/logs_are_streams_not_files/) của các sự kiện được tích tụ lại, theo tứ tự thời gian từ các dòng dữ liệu đầu ra của tất cả các tiến trình đang chạy và backing service. Các log trong định dạng thô của chúng điển hình là dạng text với mỗi sự kiện trên một dòng (mặc dù các backtrace (lần dấu ngược lại) của ngoại lệ có thể kéo dài ra nhiều dòng). Các log không có điểm đầu hay cuối cố định, nhưng lưu lượng liên tục giống như ứng dụng đang vận hành.

<dl>
<small style="color: gray">
A twelve-factor app never concerns itself with routing or storage of its output stream. It should not attempt to write to or manage logfiles. Instead, each running process writes its event stream, unbuffered, to stdout. During local development, the developer will view this stream in the foreground of their terminal to observe the app’s behavior.
</small>
</dl>

**Một ứng dụng twelve-factor không bao giờ bận tâm đến bản thân nó với sự định tuyến hoặc lưu trữ của dòng dữ liệu đầu ra của nó.** Nó không phải cố gắng ghi hay quản lý các logfile. Thay vì đó, mỗi tiến trình đang chạy ghi dòng dữ liệu sự kiện của nó, không bị cản trở, vào `stdout`. Trong quá trình phát triển, nhà phát triển sẽ xem dòng dữ liệu ngay trên terminal (của sổ dòng lệnh) của họ để quan sát hành vi của ứng dụng.

<dl>
<small style="color: gray">
In staging or production deploys, each process’ stream will be captured by the execution environment, collated together with all other streams from the app, and routed to one or more final destinations for viewing and long-term archival. These archival destinations are not visible to or configurable by the app, and instead are completely managed by the execution environment. Open-source log routers (such as Logplex and Fluentd) are available for this purpose.
</small>
</dl>

Trong các bản triển khai staging hay production, mỗi dòng dữ liệu của tiến trình sẽ được bắt bởi môi trường thực thi, được đối chiếu với tất cả các dòng dữ liệu khác từ app, và được định tuyến tới một hoặc nhiều điểm đến cuối cùng để xem và lưu trữ lâu dài. Những điểm đến lưu trữ này thì không hiển thị hay có thể cấu hình bởi ứng dụng, thay vì đó hoàn toàn được quản lý bởi môi trường thực thi. Các định tuyến log mã nguồn mở (ví dụ Logplex và Fluentd) thì khả dụng cho mục đích này.

<dl>
<small style="color: gray">
The event stream for an app can be routed to a file, or watched via realtime tail in a terminal. Most significantly, the stream can be sent to a log indexing and analysis system such as Splunk, or a general-purpose data warehousing system such as Hadoop/Hive. These systems allow for great power and flexibility for introspecting an app’s behavior over time, including:
</small>
</dl>

Dòng dữ liệu sự kiện cho một app có thể được định tuyến đến một tệp, hoặc được xem qua phần đuôi thời gian thực trong một terminal. Đáng kể nhất, dòng dữ liệu có thể được gửi đến một hệ thống lập chỉ mục sao chép và phân tích ví dụ như Splunk, hoặc một hệ thống lưu trữ dữ liệu với mục đích chung ví dụ Hadoop/Hive. Những hệ thống này cho phép sức mạnh và sự linh hoạt tuyệt vời để tìm hiểu hành vi của ứng dụng qua thời gian, bao gồm:

<dl>
<small style="color: gray">
- Finding specific events in the past.<br/>
- Large-scale graphing of trends (such as requests per minute).<br/>
- Active alerting according to user-defined heuristics (such as an alert when the quantity of errors per minute exceeds a certain threshold).
</small>
</dl>

* Tìm kiếm các sự kiện cụ thể trong quá khứ.
* Đồ thị quy mô lớn của các xu hướng (ví dụ số request trên phút).
* Kích hoạt cảnh báo theo phỏng đoán do người dùng định nghĩa (ví dụ như một cảnh báo khi số lượng lỗi trên phút vượt quá mức cho phép).

# 12. Admin processes (Các tiến trình người quản trị)

**Run admin/management tasks as one-off processes**<br/>
**Chạy các task admin/management (người quản trị/quản lý) như các tiến trình one-off (một lần)**

<dl>
<small style="color: gray">
The process formation is the array of processes that are used to do the app’s regular business (such as handling web requests) as it runs. Separately, developers will often wish to do one-off administrative or maintenance tasks for the app, such as:
</small>
</dl>

[Processes formation](#8-concurrency-đồng-thời) là một loạt các tiến trình mà được sử dụng để thực hiện công việc thường lệ của app (ví dụ xử lý các web request) khi nó chạy. Một cách riêng biệt, các nhà phát triển sẽ thường thực hiện các task mang tính quản trị hoặc bảo trì cho ứng dụng, ví dụ:

<dl>
<small style="color: gray">
- Running database migrations (e.g. manage.py migrate in Django, rake db:migrate in Rails).<br/>
- Running a console (also known as a REPL shell) to run arbitrary code or inspect the app’s models against the live database. Most languages provide a REPL by running the interpreter without any arguments (e.g. python or perl) or in some cases have a separate command (e.g. irb for Ruby, rails console for Rails).<br/>
- Running one-time scripts committed into the app’s repo (e.g. php scripts/fix_bad_records.php).
</small>
</dl>

* Chạy các database migration (di chuyển cơ sở dữ liệu) (ví dụ `manage.py migrate` trong Django, `rake db:migrate` trong Rails). Chạy một câu 
* Chạy một console (điều khiển) (hay cũng được biết như là một REPL shell) để chạy code tuỳ ý hoặc kiểm tra các mô hình ứng dụng dựa trên cơ sở dữ liệu trực tiếp. Hầu hết các ngôn ngữ cung cấp một REPL bằng việc chạy trình thông dịch mà không cần bất kỳ tham số nào (ví dụ `python` hay `perl`) hoặc trong một số trường hợp có một câu lệnh riêng biệt (ví dụ `irb` cho Ruby, `rails console` cho Rails).
* Chạy những script (kịch bản) một lần (ví dụ `php scripts/fix_bad_records.php`).

<dl>
<small style="color: gray">
One-off admin processes should be run in an identical environment as the regular long-running processes of the app. They run against a release, using the same codebase and config as any process run against that release. Admin code must ship with application code to avoid synchronization issues.
</small>
</dl>

Các tiến trình admin one-off nên được chạy trong một môi trường giống nhau như là [các tiến trình chạy dài thường lệ](#6-processes-tiến-trình) của ứng dụng. Chúng chạy dựa trên một bản phát hành, sử dụng chung codebase và cấu hình như bất kì tiến trình nào chạy dựa trên bản phát hành đó. Admin code phải được chuyển kèm với code của ứng dụng để tránh các vấn đề về đồng bộ.

<dl>
<small style="color: gray">
The same dependency isolation techniques should be used on all process types. For example, if the Ruby web process uses the command bundle exec thin start, then a database migration should use bundle exec rake db:migrate. Likewise, a Python program using Virtualenv should use the vendored bin/python for running both the Tornado webserver and any manage.py admin processes.
</small>
</dl>

Các công nghệ cô lập phụ thuộc giống nhau nên được sử dụng trên tất cả các kiểu tiến trình. Ví dụ, nếu web viết bằng Ruby sử dụng câu lệnh `bundle exec` để khởi động, sau đó một database migration nên sử dụng `bundle exec rake db:migrate`. Tương tự như vậy, một chương trình Python sử dụng Virtualenv nên sử dụng phần cung cấp `bin/python` để chạy cả máy chủ web Tornado và bất kỳ các tiến trình admin `manage.py` nào.

<dl>
<small style="color: gray">
Twelve-factor strongly favors languages which provide a REPL shell out of the box, and which make it easy to run one-off scripts. In a local deploy, developers invoke one-off admin processes by a direct shell command inside the app’s checkout directory. In a production deploy, developers can use ssh or other remote command execution mechanism provided by that deploy’s execution environment to run such a process.
</small>
</dl>

Twelve-factor ủng hộ mạnh mẽ các ngôn ngữ mà cung cấp một REPL shell bên ngoài, và nó làm cho app dễ dàng chạy những script một lần. Trong một deply local, các developer gọi các admin tiến trình một lần bằng một câu lệnh shell trực tiếp bên trong thư mục làm việc của app. Trong một bản triển khai production, những nhà phát triển có thể sử dụng `ssh` hoặc cơ chế thực thi câu lệnh từ xa khác được cung cấp bởi môi trường thực thi của bản triển khai để chạy ví dụ một tiến trình.

* [Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 1 / 2](/12-factor-p1)<br/>
* Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 2 / 2 (Đang xem)

Nguồn: https://12factor.net/

Dịch bởi: `code2080`