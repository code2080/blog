---
title: "Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 1/2"
date: 2018-09-22T00:00:00+07:00
draft: false
author: "cstd"
tags: ["software development", "translation"]
---

* Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 1 / 2 (Đang xem)<br/>
* [Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 2 / 2](/12-factor-p2)

# MỞ ĐẦU

## Giới thiệu

<dl>
<small style="color: gray">
In the modern era, software is commonly delivered as a service: called web apps, or software-as-a-service. The twelve-factor app is a methodology for building software-as-a-service apps that:
</small>
</dl>

Trong kỷ nguyên hiện đại, phần mềm thông thường được cung cấp như một dịch vụ: được gọi là *web apps* (những ứng dụng web), hay là *software-as-a-service* (phần mềm như một dịch vụ). Ứng dụng xây dựng theo twelve-factor (12-yếu tố) là một phương pháp học để xây dựng ứng dụng như vậy, bao gồm:

<dl>
<small style="color: gray">
- Use declarative formats for setup automation, to minimize time and cost for new developers joining the project;<br/>
- Have a clean contract with the underlying operating system, offering maximum portability between execution environments;<br/>
- Are suitable for deployment on modern cloud platforms, obviating the need for servers and systems administration;<br/>
- Minimize divergence between development and production, enabling continuous deployment for maximum agility;<br/>
- And can scale up without significant changes to tooling, architecture, or development practices.
</small>
</dl>

* Sử dụng các định dạng **khai báo** cho thiết lập tự động, để giảm thiểu tối đa thời gian và công sức cho những nhà phát triển mới tham gia vào dự án;
* Có **giao kèo sạch** với hệ thống vận hành ở bên dưới, đưa ra **tối đa tính khả chuyển** giữa các môi trường thực thi;
* Tiện dụng cho **việc phát triển** trên **nền tảng điện toán đám mây** hiện đại, loại bỏ sự cần thiết cho các máy chủ và những người quản trị hệ thống;
* **Giảm thiểu tối đa sự khác nhau** giữa development (môi trường phát triển) và production (môi trường sản phẩm), kích hoạt tính năng **phát triển liên tục** để tối đa tốc độ phát triển ứng dụng;
* Và có thể **mở rộng** mà không cần thay đổi đáng kể nào về công cụ, kiến trúc, hay các phương pháp phát triển.

<dl>
<small style="color: gray">
The twelve-factor methodology can be applied to apps written in any programming language, and which use any combination of backing services (database, queue, memory cache, etc).
</small>
</dl>

Phương pháp học twelve-factor có thể áp dụng cho những ứng dụng viết bằng bất cứ ngôn ngữ nào, và sử dụng bất kì sự kết hợp nào của các dịch vụ ở đằng sau (như cơ sở dữ liệu, queue (hàng đợi), memory cache (bộ nhớ đệm),...).

## Khởi nguồn

<dl>
<small style="color: gray">
The contributors to this document have been directly involved in the development and deployment of hundreds of apps, and indirectly witnessed the development, operation, and scaling of hundreds of thousands of apps via our work on the Heroku platform.
</small>
</dl>

Những người đóng góp cho tài liệu này đều đã trực tiếp gặp phải những rắc rối trong sự phát triển và triển khai hàng trăm ứng dụng, và gián tiếp chứng kiến sự phát triển, vận hành, và mở rộng của hàng trăm triệu ứng dụng thông qua công việc của chúng tôi ở nền tảng [Heroku](http://www.heroku.com/).

<dl>
<small style="color: gray">
This document synthesizes all of our experience and observations on a wide variety of software-as-a-service apps in the wild. It is a triangulation on ideal practices for app development, paying particular attention to the dynamics of the organic growth of an app over time, the dynamics of collaboration between developers working on the app’s codebase, and avoiding the cost of software erosion.
</small>
</dl>

Tài liệu này tổng hợp tất cả kinh nghiệm và sự quan sát của chúng tôi trong thế giới rộng lớn của ứng dụng software-as-a-service. Nó là một tam phân của những thực hành ý tưởng cho phát triển ứng dụng, đặc biệt chú ý đến những sự năng động của việc phát triển hữu cơ của một ứng dụng qua thời gian, những sự năng động của cộng tác giữa những nhà phát triển làm việc trên cùng mã nguồn ứng dụng, và [tránh chi phí của việc sói mòn phần mềm](http://blog.heroku.com/archives/2011/6/28/the_new_heroku_4_erosion_resistance_explicit_contracts/).

<dl>
<small style="color: gray">
Our motivation is to raise awareness of some systemic problems we’ve seen in modern application development, to provide a shared vocabulary for discussing those problems, and to offer a set of broad conceptual solutions to those problems with accompanying terminology. The format is inspired by Martin Fowler’s books Patterns of Enterprise Application Architecture and Refactoring.
</small>
</dl>

Động lực của chúng tôi là để nâng cao nhận thức về một số lỗi mang tính hệ thống mà chúng ta thấy trong phát triển ứng dụng hiện đại, để cung cấp những ngôn từ chung để thảo luận về những vấn đề đó, và để đưa ra một tập rộng các giải pháp mang tính khái niệm cho những vấn đề đó với những thuật ngữ kèm theo. Định dạng này được lấy cảm hứng bởi cuốn sách [Patterns of Enterprise Application Architecture and Refactoring](https://books.google.com/books/about/Patterns_of_enterprise_application_archi.html?id=FyWZt5DdvFkC) của Martin Fowler.

## Ai nên đọc tài liệu này?

<dl>
<small style="color: gray">
Any developer building applications which run as a service. Ops engineers who deploy or manage such applications.
</small>
</dl>

Bất kỳ nhà phát triển nào đang xây dựng ứng dụng chạy như là một dịch vụ. Các kỹ sư hệ thống, người triển khai và quản lý các ứng dụng như vậy.

# 1. Codebase (Toàn bộ mã nguồn dự án)

**One codebase tracked in revision control, many deploys**<br/>
**Một codebase thì được theo dấu trong kiểm soát sửa đổi, và có nhiều bản triển khai**

<dl>
<small style="color: gray">
A twelve-factor app is always tracked in a version control system, such as Git, Mercurial, or Subversion. A copy of the revision tracking database is known as a code repository, often shortened to code repo or just repo.
</small>
</dl>

Một ứng dụng twelve-factor thì luôn luôn được theo dấu trong một hệ thống quản lý phiên bản, ví dụ như Git, Mercurial, hoặc Subversion. Một bản sao chép của cơ sở dữ liệu theo dấu sửa đổi thì được biết như là một *code repository*, hay thường gọi tắt là *code repo* hoặc chỉ là *repo*.

<dl>
<small style="color: gray">
A codebase is any single repo (in a centralized revision control system like Subversion), or any set of repos who share a root commit (in a decentralized revision control system like Git).
</small>
</dl>

Một *codebase* có thể là bất kì một repo đơn nào (trong một hệ thống kiểm soát sửa đổi tập trung như Subversion), hoặc bất kì một tập repo nào được chia sẻ root commit (trong một hệ thống kiểm soát sửa đổi phi tập trung như Git).

<dl>
<small style="color: gray">
There is always a one-to-one correlation between the codebase and the app:<br/>
- If there are multiple codebases, it’s not an app – it’s a distributed system. Each component in a distributed system is an app, and each can individually comply with twelve-factor.<br/>
- Multiple apps sharing the same code is a violation of twelve-factor. The solution here is to factor shared code into libraries which can be included through the dependency manager.
</small>
</dl>

Luôn luôn có sự tương quan một-một giữa codebase và ứng dụng:

*One codebase maps to many deploys:*<br/>
*Mỗi codebase có nhiều triển khai:*
![alt text](/images/codebase-deploys.png "One codebase maps to many deploys")

* Nếu có nhiều codebase, thì nó không phải một ứng, mà là một hệ thống phân tán. Mỗi thành phần trong một hệ thống phân tán là một ứng dụng, và mỗi thành phần đó có thể tuân theo một cách cá nhân với twelve-factor.

* Nhiều ứng dụng chia sẻ mã giống nhau là một sự vi phạm trong twelve-factor. Giải pháp ở đây là để những phần chia sẻ code chung vào thành những thư viện, mà có thể được bao hàm thông qua [trình quản lý phụ thuộc](#2-dependencies-các-phụ-thuộc).

<dl>
<small style="color: gray">
There is only one codebase per app, but there will be many deploys of the app. A deploy is a running instance of the app. This is typically a production site, and one or more staging sites. Additionally, every developer has a copy of the app running in their local development environment, each of which also qualifies as a deploy.
</small>
</dl>

Chỉ có duy nhất một codebase với mỗi ứng dụng, nhưng sẽ có nhiều triển khai của một ứng dụng. Một *triển khai* là một instance (thực thể) của ứng dụng, điển hình nó có thể là một production site, và là một hoặc nhiều staging site. Thêm nữa, mọi nhà phát triển đều có một bản sao chép của ứng dụng chạy ở môi trường phát triển cục bộ của họ, mỗi trong số đó cũng đều được coi là một bản triển khai.

<dl>
<small style="color: gray">
The codebase is the same across all deploys, although different versions may be active in each deploy. For example, a developer has some commits not yet deployed to staging; staging has some commits not yet deployed to production. But they all share the same codebase, thus making them identifiable as different deploys of the same app.
</small>
</dl>

Codebase thì giống nhau ở tất cả các triển khai, mặc dù các phiên bản khác nhau có thể được kích hoạt ở mỗi triển khai. Ví dụ, một nhà phát triển có một số commit chưa được triển khai đến staging; staging thì cũng có một số commit chưa được triển khai đến production. Nhưng chúng đều chia sẻ codebase giống nhau, vì vậy để chúng có thể nhận biết được như là các triển khai khác nhau của cùng một ứng dụng.

# 2. Dependencies (Các phụ thuộc)

**Explicitly declare and isolate dependencies**<br/>
**Khai báo rõ ràng và cô lập các phụ thuộc**

<dl>
<small style="color: gray">
Most programming languages offer a packaging system for distributing support libraries, such as CPAN for Perl or Rubygems for Ruby. Libraries installed through a packaging system can be installed system-wide (known as “site packages”) or scoped into the directory containing the app (known as “vendoring” or “bundling”).
</small>
</dl>

Hầu hết các ngôn ngữ lập trình đều đưa ra hệ thống quản lý gói cho việc phân phối các thư viện hỗ trợ, ví dụ như CPAN cho Perl, Rubygems for Ruby, NPM cho NodeJS, Pip cho Python. Những thư viện được cài đặt thông qua một hệ thống quản lý gói có thể được cài đặt trong phạm vi toàn hệ thống (được biết như là "site packages") hoặc giới hạn phạm vi trong thư mục chứa ứng dụng (được biết như là "vendoring" hay "bundling").

<dl>
<small style="color: gray">
A twelve-factor app never relies on implicit existence of system-wide packages. It declares all dependencies, completely and exactly, via a dependency declaration manifest. Furthermore, it uses a dependency isolation tool during execution to ensure that no implicit dependencies “leak in” from the surrounding system. The full and explicit dependency specification is applied uniformly to both production and development.
</small>
</dl>

**Một ứng dụng twelve-factor không bao giờ dựa vào sự tồn tại ngầm của các gói trên toàn hệ thống**. Nó khai báo tất cả các phụ thuộc, trọn vẹn và chính xác, thông qua một bản kê *khai báo phụ thuộc*. Hơn nữa nó sử dụng một công cụ *cô lập phụ thuộc* trong quá trình thực thi, để chắc rằng không có phụ thuộc ngầm nào "bị rò rỉ vào trong" từ hệ thống xung quanh. Bản đặc tả phụ thuộc đầy đủ và rõ ràng được áp dụng đồng nhất với cả production và development.

<dl>
<small style="color: gray">
For example, Bundler for Ruby offers the Gemfile manifest format for dependency declaration and bundle exec for dependency isolation. In Python there are two separate tools for these steps – Pip is used for declaration and Virtualenv for isolation. Even C has Autoconf for dependency declaration, and static linking can provide dependency isolation. No matter what the toolchain, dependency declaration and isolation must always be used together – only one or the other is not sufficient to satisfy twelve-factor.
</small>
</dl>

Ví dụ, Bundler cho Ruby đưa ra định dạng bản kê `Gemfile` cho khai báo phụ thuộc và `bundle exec` cho cô lập phụ thuộc. Trong Python, có hai công cụ riêng biệt cho các bước này - Pip sử dụng cho khai báo, Virtualenv cho cô lập. Thậm chí C có Autoconf cho khai báo phụ thuộc, và static linking (liên kết tĩnh) có thể cung cấp cô lập denpendency. Bất kể toolchain (tập công cụ cho lập trình) nào, khai báo và cô lập phụ thuộc phải luôn luôn được sử dụng cùng nhau - chỉ cái này hoặc cái kia là không đủ thoả mãn twelve-factor.

<dl>
<small style="color: gray">
One benefit of explicit dependency declaration is that it simplifies setup for developers new to the app. The new developer can check out the app’s codebase onto their development machine, requiring only the language runtime and dependency manager installed as prerequisites. They will be able to set up everything needed to run the app’s code with a deterministic build command. For example, the build command for Ruby/Bundler is bundle install, while for Clojure/Leiningen it is lein deps.
</small>
</dl>

Một lợi ích của khai báo phụ thuộc rõ ràng là nó làm đơn giản việc thiết lập cho những nhà phát triển mới tham gia vào app. Nhà phát triển mới có thể check out codebase của ứng dụng sang máy phát triển của họ, chỉ yêu cầu có trình chạy của ngôn ngữ và trình quản lý phụ thuộc được cài đặt như điều kiện trước hết. Họ sẽ có thể thiết lập mọi thứ cần thiết để chạy mã nguồn ứng dụng với *câu lệnh build* xác định. Ví dụ, câu lệnh build cho Ruby/Bundler là `bundle install`, còn cho Clojure/leiningen là `lein deps`.

<dl>
<small style="color: gray">
Twelve-factor apps also do not rely on the implicit existence of any system tools. Examples include shelling out to ImageMagick or curl. While these tools may exist on many or even most systems, there is no guarantee that they will exist on all systems where the app may run in the future, or whether the version found on a future system will be compatible with the app. If the app needs to shell out to a system tool, that tool should be vendored into the app.
</small>
</dl>

Ứng dụng twelve-factor cũng không dựa vào tồn tại ngầm của bất kỳ công cụ hệ thống nào. Ví dụ bao gồm shelling out (truyền lệnh) đến ImageMagick hoặc `curl`. Mặc dù những công cụ này có thể tồn tại trên nhiều hoặc thậm chí hầu hết hệ thống, tuy nhiên không có gì đảm bảo rằng chúng sẽ tồn tại trên tất cả hệ thống nơi mà ứng dụng của bạn có thể chạy trong tương lai, hoặc liệu phiên bản được tìm thấy trên một hệ thống trong tương lai sẽ tương thích với ứng dụng. Nếu ứng dụng cần shell out đến một công cụ hệ thống, thì công cụ này nên được đóng gói trong ứng dụng.

# 3. Config (Cấu hình)

**Store config in the environment**<br/>
**Lưu trữ cấu hình trong môi trường**

<dl>
<small style="color: gray">
An app’s config is everything that is likely to vary between deploys (staging, production, developer environments, etc). This includes:<br/>
- Resource handles to the database, Memcached, and other backing services<br/>
- Credentials to external services such as Amazon S3 or Twitter<br/>
- Per-deploy values such as the canonical hostname for the deploy
</small>
</dl>

Một cấu hình ứng dụng là mọi thứ mà có khả năng thay đổi giữa [các bản triển khai](#1-codebase-toàn-bộ-mã-nguồn-dự-án) (staging, production, developer environments,...). Bao gồm:

* Tài nguyên xử lý cơ sở dữ liệu, Memcached, và các [dịch vụ ở đằng sau](#4-backing-services-dịch-vụ-đằng-sau)
* Uỷ quyền đến dịch vụ bên ngoài như Amazon S3 hay Twitter
* Các giá trị trên mỗi triển khai ví dụ như hostname chính thức cho bản triển khai

<dl>
<small style="color: gray">
Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.
</small>
</dl>

Các ứng dụng thỉnh thoảng lưu trữ cấu hình như hằng số trong code (mã). Điều này vi phạm twelve-factor, nó yêu cầu sự **phân chia chặt chẽ của cấu hình từ code**. Cấu hình về thực chất sẽ thay đổi theo các triển khai, code thì không.

<dl>
<small style="color: gray">
A litmus test for whether an app has all config correctly factored out of the code is whether the codebase could be made open source at any moment, without compromising any credentials.
</small>
</dl>

Một bài kiểm thử là liệu một ứng dụng có tất cả cấu hình đúng được đặt ngoài code không chính là liệu codebase có thể trở thành open source (mã nguồn mở) bất cứ lúc nào không, mà cần thoả hiệp bất cứ uỷ quyền nào.

<dl>
<small style="color: gray">
Note that this definition of “config” does not include internal application config, such as config/routes.rb in Rails, or how code modules are connected in Spring. This type of config does not vary between deploys, and so is best done in the code.
</small>
</dl>

Chú ý rằng định nghĩa này của "cấu hình" không bao gồm cho cấu hình ứng dụng bên trong, ví dụ `config/routes.rb` trong Rails, hay cách mà những mô-đun code được kết nối trong Spring. Kiểu cấu hình này không thay đổi giữa các triển khai, vì vậy tốt nhất là để nó ở trong code.

<dl>
<small style="color: gray">
Another approach to config is the use of config files which are not checked into revision control, such as config/database.yml in Rails. This is a huge improvement over using constants which are checked into the code repo, but still has weaknesses: it’s easy to mistakenly check in a config file to the repo; there is a tendency for config files to be scattered about in different places and different formats, making it hard to see and manage all the config in one place. Further, these formats tend to be language- or framework-specific.
</small>
</dl>

Một cách tiếp cận khác để cấu hình là sử dụng các tệp cấu hình mà không được đánh dấu vào trong trình kiểm soát mã nguồn, ví dụ như `config/database.yml` trong Rails. Việc này là cải thiện rất lớn thay cho việc sử dụng hằng số mà được đánh dấu trong code repo, nhưng nó vẫn có những điểm yếu; là rất dễ mắc sai lầm đánh dấu một tệp cấu hình trong repo; có một xu hướng cho những tệp cấu hình bị phân tán ở các nơi khác nhau và định dạng khác nhau, làm cho việc xem và quản lý tất cả các cấu hình ở một nơi khó khăn. Hơn nữa, những định dạng này hướng theo đặc tả của ngôn ngữ hoặc framework.

<dl>
<small style="color: gray">
The twelve-factor app stores config in environment variables (often shortened to env vars or env). Env vars are easy to change between deploys without changing any code; unlike config files, there is little chance of them being checked into the code repo accidentally; and unlike custom config files, or other config mechanisms such as Java System Properties, they are a language- and OS-agnostic standard.
</small>
</dl>

**Ứng dụng twelve-factor lưu cấu hình trong biến môi trường (thường viết ngắn gọn là *env vars* hay *env*)**. Các biến môi trường dễ dàng thay đổi giữa các triển khai mà không phải thay đổi code; không giống như tệp cấu hình, chúng chỉ có nguy cơ nhỏ không may bị đánh dấu trong code repo; và cũng không giống như tệp cấu hình tuỳ chỉnh, hay các cơ chế cấu hình khác ví dụ Java System Properties, chúng là theo chuẩn không thể biết của ngôn ngữ lập trình và hệ điều hành.

<dl>
<small style="color: gray">
Another aspect of config management is grouping. Sometimes apps batch config into named groups (often called “environments”) named after specific deploys, such as the development, test, and production environments in Rails. This method does not scale cleanly: as more deploys of the app are created, new environment names are necessary, such as staging or qa. As the project grows further, developers may add their own special environments like joes-staging, resulting in a combinatorial explosion of config which makes managing deploys of the app very brittle.
</small>
</dl>

Một mặt khác của quản lý cấu hình là đặt nhóm. Thỉnh thoảng ứng dụng nhóm cấu hình vào những nhóm được đặt tên (thông thường được gọi là “environments”), được đặt sau những triển khai xác định, ví dụ như `development`, `test`, and `production` environments trong Rails. Phương pháp này thì không mở rộng được sạch sẽ (cleanly): như khi nhiều hơn bản triển khai của ứng dụng được tạo, thì phải cần tên môi trường mới, ví dụ `staging` hay `qa`. Khi dự án lớn hơn, nhà phát triển có thêm những môi trường đặc biệt của riêng họ như `joes-staging`, kết quả là một tổ hợp lớn của cấu hình, làm cho việc quản lý các triển khai của ứng dụng rất dễ phá vỡ.

<dl>
<small style="color: gray">
In a twelve-factor app, env vars are granular controls, each fully orthogonal to other env vars. They are never grouped together as “environments”, but instead are independently managed for each deploy. This is a model that scales up smoothly as the app naturally expands into more deploys over its lifetime.
</small>
</dl>

Trong ứng dụng twelve-factor, các biến môi trường là những điều khiển gọn gàng, mỗi biến đều trực giao với biến khác. Chúng không bao giờ được nhóm lại với nhau như “environments”, thay vì đó chúng được quản lý độc lập với mỗi bản triển khai. Đây làm một mô hình mà có thể mở rộng một cách trôi chảy, như là việc ứng dụng mở rộng nhiều bản triển khai qua thời gian theo lẽ tự nhiên.

# 4. Backing services (Dịch vụ đằng sau)

**Treat backing services as attached resources**<br/>
**Coi các backing service (dịch vụ phía sau) như những attached resource (tài nguyên đính kèm)**

<dl>
<small style="color: gray">
A backing service is any service the app consumes over the network as part of its normal operation. Examples include datastores (such as MySQL or CouchDB), messaging/queueing systems (such as RabbitMQ or Beanstalkd), SMTP services for outbound email (such as Postfix), and caching systems (such as Memcached).
</small>
</dl>

Một *dịch vụ ở đằng sau* là bất kì một dịch vụ nào mà ứng dụng cần kết nối dùng thông qua mạng như một phần vận hành bình thường của nó. Ví dụ gồm datastores (như MySQL hay CouchDB), các hệ thống messaging/queuing (ví dụ như RabbitMQ hay Beanstalkd), các dịch vụ SMTP để gửi email ra ngoài (ví dụ Postfix), và hệ thống caching (Ví dụ Memcached).

<dl>
<small style="color: gray">
Backing services like the database are traditionally managed by the same systems administrators as the app’s runtime deploy. In addition to these locally-managed services, the app may also have services provided and managed by third parties. Examples include SMTP services (such as Postmark), metrics-gathering services (such as New Relic or Loggly), binary asset services (such as Amazon S3), and even API-accessible consumer services (such as Twitter, Google Maps, or Last.fm).
</small>
</dl>

Những dịch vụ ở đằng sau như cơ sở dữ liệu thì được quản lý một cách truyền thống bởi cùng các quản trị hệ thống giống như bản triển khai runtime của ứng dụng. Ngoài các dịch vụ được quản lý cục bộ này, ứng dụng cũng có thể có những dịch vụ được cung cấp và quản lý bởi bên thứ ba. Ví dụ bao gồm các dịch vụ SMTP (như Postmark), các dịch vụ thu thập số liệu (ví dụ như New Relic hay Loggly), các dịch vụ tài sản nhị phân (Ví dụ Amazon S3), và thậm chí các dụ vụ tiêu dùng qua API (như Twitter, Google Maps, hay Last.fm).

<dl>
<small style="color: gray">
The code for a twelve-factor app makes no distinction between local and third party services. To the app, both are attached resources, accessed via a URL or other locator/credentials stored in the config. A deploy of the twelve-factor app should be able to swap out a local MySQL database with one managed by a third party (such as Amazon RDS) without any changes to the app’s code. Likewise, a local SMTP server could be swapped with a third-party SMTP service (such as Postmark) without code changes. In both cases, only the resource handle in the config needs to change.
</small>
</dl>

**Code cho một ứng dụng twelve-factor không phân biệt giữa các dịch vụ cục bộ và bên thứ ba**. Để ứng dụng, cả dịch cục bộ lẫn bên thứ ba đều là tài nguyên đính kèm, được truy cập thông qua một URL hoặc locator/credentials khác lưu trong trong [cấu hình](#3-config-cấu-hình). Một [bản triển khai](#1-codebase-toàn-bộ-mã-nguồn-dự-án) của ứng dụng twelve-factor sẽ có thể được hoán đổi từ một cơ sở dữ liệu MySQL cục bộ với một cơ sở dữ liệu quản lý bởi bên thứ ba (ví dụ Amazon RDS) mà không cần thay đổi code của ứng dụng. Tương tự như vậy, một máy chủ SMTP cục bộ có thể được hoán đổi với một dịch vụ SMTP của bên thứ ba (ví dụ Postmark) mà không phải thay đổi code. Trong cả hai trường hợp, chỉ tài nguyên xử lý trong cấu hình là cần thay đổi.

<dl>
<small style="color: gray">
Each distinct backing service is a resource. For example, a MySQL database is a resource; two MySQL databases (used for sharding at the application layer) qualify as two distinct resources. The twelve-factor app treats these databases as attached resources, which indicates their loose coupling to the deploy they are attached to.
</small>
</dl>

Mỗi dịch vụ ở đằng sau riêng biệt là một *tài nguyên*. Ví dụ, một cơ sở dữ liệu MySQL là một tài nguyên, hai cơ sở dữ liệu MySQL (sử dụng phân mảnh ở tầng ứng dụng) được xác định là hai tài nguyên riêng biệt. Ứng dụng twelve-factor coi những cơ sở dữ liệu này như những tài nguyên đính kèm, nó chỉ ra khớp nối lỏng lẻo của chúng với bản triển khai mà chúng được gắn vào.

*A production deploy attached to four backing services:*<br/>
*Một bản triển khai production được gắn vào bốn backing service:*
![alt text](/images/attached-resources.png "A production deploy attached to four backing services")

<dl>
<small style="color: gray">
Resources can be attached to and detached from deploys at will. For example, if the app’s database is misbehaving due to a hardware issue, the app’s administrator might spin up a new database server restored from a recent backup. The current production database could be detached, and the new database attached – all without any code changes.
</small>
</dl>

Những tài nguyên có thể được gắn vào hoặc tách ra từ các bản triển khai nếu muốn. Ví dụ, nếu CSDL của ứng dụng đang bị lỗi do sự cố phần cứng, người quản trị ứng dụng có thể xoay sang một máy chủ cơ sở dữ liệu mới được khôi phục từ bản sao lưu gần nhất. Cơ sở dữ liệu của bản production hiện tại có thể bị tháo ra, và cơ sở dữ liệu mới được gắn vào mà không phải thay đổi bất kì dòng code nào.

# 5. Build, release, run (Đóng gói, phát hành, chạy)

**Strictly separate build and run stages**<br/>
**Phân chia rõ ràng giữa giai đoạn đóng gói và chạy**

<dl>
<small style="color: gray">
A codebase is transformed into a (non-development) deploy through three stages:<br/>
- The build stage is a transform which converts a code repo into an executable bundle known as a build. Using a version of the code at a commit specified by the deployment process, the build stage fetches vendors dependencies and compiles binaries and assets.<br/>
- The release stage takes the build produced by the build stage and combines it with the deploy’s current config. The resulting release contains both the build and the config and is ready for immediate execution in the execution environment.<br/>
- The run stage (also known as “runtime”) runs the app in the execution environment, by launching some set of the app’s processes against a selected release.<br/>
</small>
</dl>

Một codebase được chuyển đổi vào một bản triển khai (không phải môi trường development) thông qua ba giai đoạn:

* *Giai đoạn đóng gói* là một chuyển đổi mà chuyển từ code repo thành một gói có thể thực thi gọi là một build. Sử dụng một phiên bản của code ở một commit được chỉ định bởi tiến trình phát triển, giai đoạn đóng gói sẽ lấy các phụ thuộc của bên cung cấp và biên dịch thành chuỗi các chuỗi nhị phân và tài sản.
* *Giai đoạn phát hành* sẽ lấy bản đóng gói được tạo từ giai đoạn đóng gói và kết hợp với cấu hình hiện tại của triển khai. *Phát hành* nhận được bao gồm cả bản đóng gói và cấu hình đã sẵn sàng cho việc thực thi ngay trong môi trường thực thi.
* *Giai đoạn chạy* (cũng được biết như là "runtime") chạy ứng dụng trong môi trường thực thi, bằng việc chạy một số tập tiến trình ứng dụng đối với bản phát hành đã chọn.

*Code becomes a build, which is combined with config to create a release:*<br/>
*Code trở thành một đóng gói, sau đó sẽ được kết hợp với cấu hình để tạo ra một bản phát hành:*
![alt text](/images/release.png "Code becomes a build, which is combined with config to create a release")

<dl>
<small style="color: gray">
The twelve-factor app uses strict separation between the build, release, and run stages. For example, it is impossible to make changes to the code at runtime, since there is no way to propagate those changes back to the build stage.
</small>
</dl>

**Ứng dụng twelve-factor sử dụng phân chia chặt chẽ giữa các giai đoạn đóng gói, phát hành và chạy.** Ví dụ, không thể thay đổi code ở runtime, vì không có cách nào để truyền những thay đổi này trở lại giai đoạn đóng gói.

<dl>
<small style="color: gray">
Deployment tools typically offer release management tools, most notably the ability to roll back to a previous release. For example, the Capistrano deployment tool stores releases in a subdirectory named releases, where the current release is a symlink to the current release directory. Its rollback command makes it easy to quickly roll back to a previous release.
</small>
</dl>

Công cụ phát triển thường đưa ra công cụ quản lý phát hành, đáng kể nhất là khả năng có thể khôi phục về một bản phát hành trước đó. Ví dụ công cụ phát triển Capistrano lưu các phát hành ở một thư mục con đặt tên là `releases`, nơi mà bản phát hành hiện tại là một đường dẫn đến thư mục phát hành hiện tại. Câu lệnh khôi phục của nó làm cho việc khôi phục trở về các bản phát hành trước một cách dễ dàng.

<dl>
<small style="color: gray">
Every release should always have a unique release ID, such as a timestamp of the release (such as 2011-04-06-20:32:17) or an incrementing number (such as v100). Releases are an append-only ledger and a release cannot be mutated once it is created. Any change must create a new release.
</small>
</dl>

Mọi bản phát hành luôn luôn nên có một ID duy nhất, ví dụ như sử dụng timestamp (mốc thời gian) phát hành (như `2011-04-06-20:32:17`) hoặc một số tăng dần (ví dụ `v100`). Các bản phát hành là một cuốn sổ cái chỉ cho thêm vào và các bản phát hành, không thể sửa đổi một khi nó đã được tạo. Chỉ có thể thay đổi bằng cách thêm một phát hành mới.

<dl>
<small style="color: gray">
Builds are initiated by the app’s developers whenever new code is deployed. Runtime execution, by contrast, can happen automatically in cases such as a server reboot, or a crashed process being restarted by the process manager. Therefore, the run stage should be kept to as few moving parts as possible, since problems that prevent an app from running can cause it to break in the middle of the night when no developers are on hand. The build stage can be more complex, since errors are always in the foreground for a developer who is driving the deploy.
</small>
</dl>

Các bản đóng gói được khởi tạo bởi nhà phát triển ứng dụng khi code mới được triển khai. Trong thực thi runtime, có thể tự động xảy ra một số trường hợp ví dụ như việc khởi động lại máy chủ, hoặc một tiến trình bị sập được khởi động lại bởi trình quản lý tiến trình. Vì vậy, giai đoạn chạy nên được giữ thành một vài bước có thể, vì những trục trặc cản trở một ứng dụng đang chạy có thể là nguyên nhân làm cho ứng dụng bị ngắt giữa đêm khi mà không có một nhà phát triển nào đang ở đó. Giai đoạn đóng gói có thể phức tạp hơn, vì những lỗi luôn luôn cận kề đối với nhà phát triển đang tạo bản triển khai.

* Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 1 / 2 (Đang xem)<br/>
* [Xây dựng ứng dụng theo 12 yếu tố (Twelve-Factor App) - Phần 2 / 2](/12-factor-p2)

Nguồn: https://12factor.net/

Dịch bởi: `code2080`