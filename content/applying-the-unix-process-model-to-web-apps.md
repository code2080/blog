---
title: "Áp dụng mô hình Process Model vào ứng dụng Web"
date: 2018-09-26T00:00:00+07:00
draft: false
author: "cstd"
tags: ["software development"]
---

Để chúng ta có thể hiểu thêm về process (tiến trình), process là gì, tôi đã dịch thêm bài này *Applying the Unix Process Model to Web Apps* của Adam Wiggins, nguồn được lưu ở cuối bài.

<dl>
<small style="color: gray">
The unix process model is a simple and powerful abstraction for running server-side programs. Applied to web apps, the process model gives us a unique way to think about dividing our workloads and scaling up over time.
</small>
</dl>

Mô hình unix process là một sự trừu tượng cho các chương trình đang chạy ở vùng máy chủ. Áp dụng vào ứng dụng web, mô hình process mang lại cho chúng ta một cách độc nhất để nghĩ việc phân chia các workload và mở rộng chúng theo thời gian.

# Process model basics (Các khái niệm cơ bản của mô hình process)

<dl>
<small style="color: gray">
Let’s begin with a simple illustration of the basics of the process model, using a well-known unix daemon: memcached.
</small>
</dl>

Chúng ta cùng bắt đầu với một minh hoạ đơn giản về các khái niệm cơ bản của mô hình process, sử dụng một unix daemon quen thuộc là: memcached.

Tải về và biên dịch nó:

```
$ wget http://memcached.googlecode.com/files/memcached-1.4.5.tar.gz
$ tar xzf memcached-1.4.5.tar.gz 
$ cd memcached-1.4.5
$ ./configure
$ make
```

Chạy chương trình:

```
$ ./memcached -vv
...
<17 server listening (auto-negotiate)
<18 send buffer was 9216, now 3728270
```

Chương trình đang chạy này được gọi là một process.

<dl>
<small style="color: gray">
Running manually in a terminal is fine for local development, but in a production deployment we want memcached to be a managed process. A managed process should run automatically when the operating system starts up, and should be restarted if the process crashes or dies for any reason.
</small>
</dl>

Chạy bằng tay trong một cửa sổ dòng lệnh là ổn trong môi trường phát triển cục bộ, nhưng trong một bản triển khai production, chúng ta muốn memcached trở thành một process được quản lý. Một process được quản lý phải được chạy tự động khi hệ điều hành khởi động, và phải được khởi động lại khi process bị sập hoặc chết vì bất cứ lý do nào.

<dl>
<small style="color: gray">
We can use a process manager to put processes under management. There are many process managers, but operating systems usually have defaults. On OS X, launchd is the built-in process manager; on Ubuntu, Upstart the built-in process manager.
</small>
</dl>

Chúng ta có thể sử dụng một trình quản lý process để đưa các process vào sự quản lý. Có nhiều trình quản lý process, nhưng hệ điều hành thường xuyên sử dụng các mặc định. Trên OS X, launchd là trình quản lý process built-in, trên Ubuntu là Upstart.

<dl>
<small style="color: gray">
Let’s set up memcached to run as a managed process on Ubuntu. Write an Upstart config:
</small>
</dl>

Chúng ta cùng thiết lập memcached chạy như một process được quản lý trong Ubuntu. Viết một cấu hình Upstart như sau:

```
/etc/init/memcached.conf
description "Memcached"
exec /usr/bin/memcached >> /var/log/memcached.log
start on runlevel [345]
respawn
```

<dl>
<small style="color: gray">
We can now tell Upstart to start our process for the first time:
</small>
</dl>

Bây giờ chúng ta có thể cho Upstart chạy process của chúng ta ở lần đầu tiên:

```
$ start memcached
memcached start/running, process 1212
```

<dl>
<small style="color: gray">
The memcached process is now running in the background, managed by the process manager, with its output stream going to /var/log/memcached.log.
</small>
</dl>

Memcached process bây giờ đang chạy nền, được quản lý bởi trình quản lý process, với dòng dữ liệu đầu ra sẽ lưu tại /var/log/memcached.log.

<dl>
<small style="color: gray">
Now that we’ve established a baseline for the process model, we can put its principles to work in more novel way: running a web app.
</small>
</dl>

Lúc này chúng ta đã thiết lập một đường cơ sở cho mô hình process, chúng ta có thể đặt các nguyên tắc làm việc của nó để làm việc theo một cách mới lạ hơn: chạy một ứng dụng web.

# Mapping the unix process model to web apps (Ánh xạ mô hình unix process vào ứng dụng web)

<dl>
<small style="color: gray">
A server daemon like memcached has a single entry point, meaning there’s only one command you run to invoke it. Web apps, on the other hand, typically have two or more entry points. Each of these entry points can be called a process type.
</small>
</dl>

Một server deamon như memcached chỉ có một đầu vào, nghĩa là chỉ có một câu lệnh bạn chạy để gọi nó. Ứng dụng web, mặt khác, thường có hai hoặc nhiều hơn các đầu vào. Mỗi đầu vào này có thể được gọi là một kiểu process.

<dl>
<small style="color: gray">
A basic Rails app will typically have two process types: a Rack-compatible web process (such as Webrick, Mongrel, or Thin), and a worker process using a queueing library (such as Delayed Job or Resque). For example:
</small>
</dl>

Một ứng dụng Rails cơ bản thường sẽ có hai kiểu process: a Rack-compatible web process (ví dụ như Webrick, Mongrel, hay Thin), và một worker process sử dụng một thư viện hàng đợi (ví dụ như Delayed Job hay Resque). Ví dụ:

|Kiểu process|Câu lệnh|
|---|---|
|web|bundle exec rails server|
|worker|bundle exec rake jobs:work|

<dl>
<small style="color: gray">
A basic Django app looks strikingly similar: a web process can be run with the manage.py admin tool; and background jobs via Celery.
</small>
</dl>

Một ứng dụng Django cơ bản trông khá tương tự, một web process có thể được chạy với công cụ người quản trị manage.py; và các job nền thông qua Celery.

|Kiểu process|Câu lệnh|
|---|---|
|web|python manage.py runserver|
|worker|celeryd --loglevel=INFO|

<dl>
<small style="color: gray">
Process types differ for each app. For example, some Rails apps use Resque instead of Delayed Job, or have multiple types of workers. Every app needs to declare its own process types.
</small>
</dl>

Các kiểu process khác nhau với mỗi ứng dụng. Ví dụ, một số ứng dụng Rails sử dụng Resque thay vì Delayed Job, hay có nhiều loại worker. Mọi ứng dụng đều cần khai báo các kiểu process của chúng.

<dl>
<small style="color: gray">
Declaration of process types is conceptually similar to declaration of dependencies. In the Ruby world, Gem Bundler and the Gemfile give us a declarative, canonical way to specify the gem dependencies for an app. We need the equivalent of Gemfile and Bundler, but for process types.
</small>
</dl>

Việc khai báo các kiểu process về khái niệm tương tự với việc khai báo các phụ thuộc. Trong thế giới Ruby, Gem Bundler và Gemfile đưa cho chúng ta một cách có tính khai báo và chính tắc để chỉ định các phụ thuộc gem cho một ứng dụng. Chúng ta cần sự tương đương của Gemfile và Bundler, nhưng là cho các kiểu process.

# Procfile, a format to declare your process types (một định dạng để khai báo các kiểu process của bạn)

<dl>
<small style="color: gray">
Procfile is an extremely simple file format which allows you to declare the process types your app uses. Its format is one process type per line, with each line formatted as:
</small>
</dl>

Procfile là một định dạng tệp cực kỳ đơn giản, cho phép bản khai báo các kiểu process mà ứng dụng sử dụng. Định dạng của nó là mỗi process trên một dòng, với mỗi dòng được định dạng như sau:

```
<process type>: <command>
```

Một ứng dụng Rails có thể có một Procfile như thế này:

```
web:    bundle exec rails server -p $PORT
worker: bundle exec rake jobs:work
```

<dl>
<small style="color: gray">
One purpose for this is structured documentation - a developer can view the Procfile to see the app’s process architecture, just as they can view the Gemfile to see its dependencies. But the greater utility of Procfile lies in our ability to parse the file and run the app’s processes automatically.
</small>
</dl>

Một mục đích khác cho việc này là tài liệu được cấu trúc - một nhà phát triển có thể xem Profile để biết kiến trúc process của ứng dụng, như việc họ có thể xem Gemfile để biết các phụ thuộc của ứng dụng. Nhưng tiện ích hơn là Procfile dựa trên khả năng của chúng ta để phân tách tệp và chạy các process của ứng dụng một cách tự động.

# Foreman, a process manager for local development (một trình quản lý process ở môi trường phát triển cục bộ)

<dl>
<small style="color: gray">
Foreman is a handy command-line tool written by David Dollar. It reads your Procfile and runs one process for each process type declared by your app.
</small>
</dl>

Foreman là một công cụ dòng lệnh thuận tiện được viết bởi David Dollar. Nó đọc Profile của bạn và chạy một process với mỗi kiểu process được khai báo bởi ứng dụng của bạn.

Cài đặt nó:

```
$ gem install foreman
```

<dl>
<small style="color: gray">
If you’ve written a Procfile (such as the one shown in the previous section) and put it in the root of your app, you can now run it like this:
</small>
</dl>

Nếu bạn viết một Procfile (ví dụ như đã viết ở phần trước) và đặt nó trong thư mục gốc của ứng dụng của bạn, bạn có thể chạy nó ngay như thế này:

![alt text](/images/foreman-screenshot.png "Foreman screenshot")

<dl>
<small style="color: gray">
Foreman runs one process for each process type that we’ve declared. Once running, the output streams for each running process are conveniently interleaved in the foreground on our terminal. Each line is prefixed with a timestamp and the name of the running process, and color-coded by which process emitted which line.
</small>
</dl>

Foreman chạy một process cho mỗi kiểu mà chúng ta đã khai báo. Khi đang chạy, các dòng dữ liệu ra với mỗi process đang chạy được xếp chồng một cách thuận tiện ngay trên cửa sổ dòng lệnh của chúng ta. Mỗi dòng được tiền tố với một mốc thời gian và tên của process đang chạy, và được tô màu ở mỗi dòng ứng với process được gọi.

<dl>
<small style="color: gray">
Foreman is a process manager in the same sense as launchd or Upstart, but tailored to the needs of app development. It runs only a single app at a time, with all processes in the foreground, and terminates if any process crashes or if you press Ctrl-C.
</small>
</dl>

Foreman là một trình quản lý process giống như launchd hay Upstart, nhưng phù hợp với như cầu của môi trường phát triển ứng dụng. Nó chạy chỉ một ứng dụng tại một thời điểm, với tất cả các process đều được hiển thị, và ngắt kết nếu bất kì process nào bị sập hoặc nếu bạn ấn Ctrl-C

# Using Procfile for deployment (Sử dụng Procfile cho việc triển khai)

<dl>
<small style="color: gray">
Bundler has a --deployment command-line option, allowing you to use your app’s Gemfile to set up gems on your production server. Procfile and Foreman can be used in a similar fashion, using a feature of Foreman to export to a process manager format of your choice.
</small>
</dl>

Bundler có tuỳ chọn dòng lệnh cho việc triển khai, cho phép bạn sử dụng Gemfile ứng dụng của bạn để thiết lập các gem cho máy chủ production của bạn. Procfile và Foreman có thể được sử dụng trong một dạng giống nhau, sử dụng một tính năng của Foreman để xuất ra một định dạng của trình quản lý process mà bạn lựa chọn.

<dl>
<small style="color: gray">
For example, let’s deploy a Procfile-backed Rails app to an Ubuntu server, selecting Upstart as the export format. As root, run the following from wherever your Procfile is located:
</small>
</dl>

Ví dụ, chúng ta hãy triển khai một ứng dụng Rails có Procfile đến một máy chủ Ubuntu, chọn Upstart làm định dạng xuất. Với quyền root, chạy các lệnh dưới dây ở nơi mà bạn đặt Procfile:

```
$ foreman export upstart /etc/init
[foreman export] writing: /etc/init/myapp.conf
[foreman export] writing: /etc/init/myapp-web.conf
[foreman export] writing: /etc/init/myapp-web-1.conf
[foreman export] writing: /etc/init/myapp-worker.conf
[foreman export] writing: /etc/init/myapp-worker-1.conf
$ start
myapp start/running, process 28572
```

<dl>
<small style="color: gray">
Your app is now running as two managed processes. You can use all of Upstart’s control capabilities, such as restarting the app when deploying a new release of your code:
</small>
</dl>

Bây giờ ứng dụng của bạn đang chạy như hai process được quản lý. Bạn có thể sử dụng tất cả khả năng điều kiển của Upstart, ví dụ khởi động lại ứng dụng khi triển khai một bản phát hành mới của code của bạn:

```
$ restart myapp
myapp start/running, process 28591
```

# Process types vs processes (Tương quan các kiểu process với các process)

<dl>
<small style="color: gray">
To scale up, we’ll want full grasp of the relationship between process types and processes.
</small>
</dl>

Để mở rộng, bạn sẽ muốn nắm đẩy đủ mối quan hệ giữa các kiểu process và các process.

<dl>
<small style="color: gray">
A process type is the prototype from which one or more processes are instantiated. This is similar to the way a class is the prototype from which one or more objects are instantiated in object-oriented programming.
</small>
</dl>

Một kiểu process là nguyên mẫu từ một hoặc nhiều process được khởi tạo. Việc này tương tự như các một lớp là nguyên mẫu từ một hoặc nhiều đối tượng được khởi tạo trong lập trình hướng đối tượng.

<dl>
<small style="color: gray">
Here’s a visual aid showing the relationship between processes (on the vertical axis) and process types (on the horizontal axis):
</small>
</dl>

Đây là một trực quan giúp hiển thị mối quan hệ giữa các process (trên trục tung) và các kiểu process (trên trục hoành):

![alt text](/images/process-types.png "Scale is expressed as running processes, workload diversity is expressed as process types")

<dl>
<small style="color: gray">
Processes, on the vertical axis, are scale. You increase this direction when you need to scale up your concurrency for the type of work handled by that process type. Foreman lets you specify concurrency for each process type when you export with the -c option. To get a process formation matching the diagram, you’d use this command:
</small>
</dl>

Các process trên trục tung là sự mở rộng. Bạn tăng theo hướng này khi bạn cần mở rộng lượng đồng thời cho kiểu công việc cần được xử lý bởi kiểu process đó. Foreman cho phép bạn chỉ định lượng đồng thời trên kiểu process khi bạn xuất với tuỳ chọn -c. Để lấy một process formation khớp với giản đồ, bạn sẽ sử dụng câu lệnh này:

```
$ foreman export upstart /etc/init -c web=2 -c worker=4 -c clock=1
```

<dl>
<small style="color: gray">
Process types, on the horizontal axis, are workload diversity. Each process type specializes in a certain type of work.
</small>
</dl>

Kiểu process theo trục hoành là sự đa dạng của workload. Mỗi kiểu process chuyên cho chính xác một kiểu công việc.

<dl>
<small style="color: gray">
For example, some apps have two types of workers, one for urgent jobs and another for long-running jobs. By subdividing into more specialized workers, you can get better responsiveness on your urgent jobs and more granular control over how to spend your compute resources.
</small>
</dl>

Ví dụ, một số ứng dụng có hai kiểu của các worker, một cho những job khẩn cấp và cái còn lại cho job chạy thời gian dài. Bằng việc chia nhỏ hơn ra nhiều worker chuyên biệt, bạn có thể nhận được phản hồi tốt hơn trên những job khẩn cấp và nhiều khả năng kiểm soát chi tiết hơn về cách chi tiết tài nguyên tính toán.

<dl>
<small style="color: gray">
Scheduling work at a certain time of day (e.g., the equivalent of cron) can be achieved with a specialized process type: a library like resque-scheduler or Clockwork can be run as a singleton process for a very flexible cron replacement. Consuming the Twitter streaming API is another type of specialized work best served by a singleton process.
</small>
</dl>

Lập lịch công việc vào một thời điểm nhất định trong ngày (ví dụ, tương đương với cron) có thể đạt được với một kiểu process chuyên biệt: một thư viện như resque-scheduler hay Clockwork có thể được chạy như một process đơn lẻ thay thế cho một cron linh hoạt. Tiêu thụ Twitter streaming (phát trực tuyến) API là một kiểu khác của công việc chuyên biệt được phục vụ tốt nhất bởi một process đơn lẻ.

<dl>
<small style="color: gray">
Pulling all of these potential use cases together, here’s an example of a Procfile for an app with five process types: a Sinatra web app, two types of Resque workers, a singleton clock with Clockwork, and a singleton ruby script consuming the Twitter streaming API:
</small>
</dl>

Lấy tất cả các trường hợp có khả năng sử dụng đến lại, đây là ví dụ của một Profile cho một ứng dụng với năm kiểu process: một ứng dụng web Sinatra, hai kiểu là Resque worker, một clock đơn lẻ với Clockwork, và một kịch bản ruby đơn lẻ tiêu thụ Twitter streaming API:

```
web:          bundle exec ruby web.rb -p $PORT
fastworker:   QUEUE=urgent bundle exec rake resque:work
slowworker:   QUEUE=*      bundle exec rake resque:work
clock:        bundle exec clockwork clock.rb
tweetscan:    bundle exec ruby tweetscan.rb
```

<dl>
<small style="color: gray">
When we run this Procfile with Foreman, we’ll give five processes - one for each process type. In production, we can use Foreman’s concurrency argument to fan out to dozens or even hundreds of running processes, potentially spread out across multiple machines.
</small>
</dl>

Khi chúng ta chạy Procfile này với Foreman, chúng ta sẽ đưa năm process - một cho mỗi kiểu. Trong production, chúng ta có thể sử dụng tham số đồng thời của Foreman phân ra hàng tá hoặc thậm chí hàng trăm process đang chạy, có khả năng trải rộng trên nhiều máy tính.

# Conclusion (Kết luận)

<dl>
<small style="color: gray">
The unix process model is a powerful way to approach running your web app. Procfile gives us a way to declare process types, and Foreman gives us an easy way to run the app’s processes in both development and deployment environments.
</small>
</dl>

Mô hình unix process là một phương pháp mạnh mẽ để tiếp cận chạy ứng dụng web của bạn. Procfile mang lại cho chúng ta một các khai báo kiểu process, và Foreman mang lại cho chúng là một cách dễ dàng để chạy các process của ứng dụng trong cả môi trường phát triển và triển khai.

Nguồn: https://adam.herokuapp.com/past/2011/5/9/applying_the_unix_process_model_to_web_apps/

Dịch bởi: `code2080`