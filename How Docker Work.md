Kernel có tác dụng gì: 
- Phản hồi các thông điệp từ phần cứng
- Khởi tạo và đặt lịch cho các chương trình
- Quản lý và hệ thống các tác vụ
- Truyền tin giữa các chương trình
- Phân chia tài nguyên, bộ nhớ, CPU, mạng, ...
- Tạo container bằng cách chỉnh thiết lập kernel

Docker là gì:
- Một chương trình viết bằng ngôn ngữ Go
- Quản lý các đặc tính của kernel
	+ Docker sử dụng cgroup(control group) để nhóm các tiến trình lại với 	nhau và bao lấy các tiến trình cùng nhóm trong một khoảng không gian ảo riêng. Chính vì vậy nên các container không can thiệp lẫn nhau 	được
	+ Docker sử dụng namespace(một đặc điểm của kernel linux) cho phép 	chia tách các tầng network. Vì vậy nên bạn có địa chỉ cho container thứ nhất và địa chỉ khác cho container thứ hai và cả địa chỉ cho 	những thứ không ở trong container nào
	+ Docker sử dụng cơ chế copy-on-write để tạo nên định nghĩa về image và nói rằng "bạn có image này, nó không thay đổi, nhưng bạn có thể 	chạy những thứ khác trên nó
- Docker mang tới sự đơn giản hoá trong việc viết script cho các hệ thống phân tán

Socket điều khiển của Docker
- Docker bao gồm 2 phần: client và server, 2 phần này giao tiếp với nhau thông qua socket, có thể là network socket ở máy mà client đang chạy, còn server có thể chạy ở một computer khác trên cloud. Hoặc cũng có thể cả client và server cùng chạy trên một máy.

- Khi chúng ta tạo một container, client sẽ gửi một thông điệp tới server với lệnh tạo mới, chạy hoặc dừng một container.

- Khi client và server ở chung một máy, chúng có thể kết nối với nhau thông qua một file đặc biệt được gọi là socket. Và vì chúng kết nối với nhau thông qua một file, docker có thể dễ dàng chia sẻ file này giữa host và container

![Docker Image](/images/docker_1.png)


Kiến trúc Docker:
- Kiến trúc client - server
- 3 thành phần chính
	+ Docker Client
	+ Docker Host
	+ Docker Registry (Hub)
- Docker Deamon nhận lệnh từ Docker Client thông qua CLI hoặc RestAPI
- Docker Client ở trên cùng host hoặc khác host với Docker Daemon
- Docker Hub: dịch vụ lưu trữ, chia sẻ Image

Docker Hub là gì:
- Là dịch vụ registry trên cloud
- Kết nối tới code repository, build, test và deploy image
- Cung cấp tài nguyên một cách tập trung cho việc tìm kiếm image, phân phối và quản lý các thay đổi của image
- Devops và developer có thể dùng các image một cách tự động và theo flow

Docker Hub cung cấp các tính năng sau:
- Image repository: tìm kiếm, lưu trữ, push và pull image cho cộng đồng người dùng docker và các image dành riêng cho sản phẩm
- Build tự động: build những image khi có sự thay đổi code của sản phẩm
- Webhook: là một tính năng tự động build, webhook thông báo cho bạn biết khi  có push thành công lên một repository
- Tổ chức: tạo nhóm làm việc để quản lý truy cập vào image repository
- Tích hợp với github và bitbucket

Quy trình của Docker: 
- Mỗi bản build tạo ra một image nhất định
- Container là một instance của image

Vòng đời của Docker:
- Khởi nguồn từ 1 image
- Image là một snapshot cố định, có sẵn mọi thứ để sẵn sàng sử dụng
- Khi bạn chạy một container và thay đổi những thứ bên trong, sẽ có một process đi cùng với container đó. Khi process đó bị dừng thì những thứ bên trong container vẫn được giữ lại.


Cơ chế lưu trữ của Docker
- Docker sử dụng cơ chế lưu trữ copy-on-write
- Tạo ra một hệ thống mới ngay lập tức mà không cần copy tất cả file hệ thống
- Hệ thống lưu lại mỗi lần thay đổi
- Vùng lưu trữ của image hoàn toàn khác với vùng lưu trữ của container. container có vùng lưu trữ riêng để ghi lại những thay đổi


Chạy các tiến trình trong container:
- Docker run:
	+ Docker run khởi tạo một container bằng cách truyền vào tên một image và một tiến 	trình để chạy container, đó là chính là tiến trình chính của container
	+ Container sẽ dừng khi tiến trình chính kết thúc
	+ Container có thể được đặt tên, nếu bạn không đặt tên thì nó sẽ tự sinh ra một 	cái tên ngẫu nhiên
- Docker attach:
	+ Truy cập contain đang chạy thông qua ID hoặc tên
	+ Có thể truy cập vào cùng một container từ nhiều nơi tại cùng một thời điểm
	+ Ctrl + P hoặc Ctrl + Q để ra khỏi container mà vẫn để tiến trình chạy
- Docker exec:
	+ Khởi tạo một process khác bên trong container đã có và sẽ trỏ tới cùng một 		container
	+ Tiện lợi cho việc debug và quản lý database
	+ Không thể tăng thêm PORT hoặc VOLUME, ...

Một số lệnh thường dùng trong container:
docker create:
	+ Tạo ra một container với các config tương tự như khi chạy với docker run
	+ Tuy nhiên container không được chạy ngay từ đầu mà bạn phải dùng lệnh docker 		start để chạy container
	+ Lệnh docker create phù hợp để setup một container với cấu hình có sẵn và khi nào 	cần sử dụng đến nó thì mới chạy
docker start/stop/restart:
	+ Thay đổi trạng thái của container, từ running sang stopped hoặc khởi động lại 	container
	+ docker stop sẽ gửi tín hiệu SIGTERM để yêu cầu tắt tiến trình, nếu sau khi gửi 	tín hiệu SIGTERM mà tiến trình không tắt thì sẽ gửi tín hiệu SIGKILL để buộc tiến 	trình tắt. SIGKILL không thể bị can thiệp nên nó sẽ tạo ra một sự kết thúc đột 		ngột cho tiến trình đang chạy
docker cp:
	+ Copy dữ liệu (file/folder) từ container tới local machine và ngược lại
	+ Có thể áp dụng cho cả container stopped và running container
	+ Đường dẫn là relative
docker inspect: 
	+ Kiểm tra các thiết lập của docker (network, driver)
	+ In ra các thông tin dưới dạng JSON
	+ Lệnh docker inspect sẽ nhận vào id của container mà bạn muốn xem
docker rm: 
	+ Dùng để xoá container dựa vào tên hoặc ID
	+ Cần stop container trước khi xoá bỏ
docker logs: 
	+ Lệnh docker logs thêm vào tên của container sẽ cho ra output của container đó
	+ Không nên để cho dung lượng log phình quá to

CHÚ Ý: Với những container không còn dùng tới nữa thì nên dừng (docker kill) và xoá nó đi (docker rm)

giới hạn tài nguyên trong docker:
	+ docker có khả năng giới hạn tài nguyên cho mỗi container
	+ Giới hạn memory: 
		docker run --memory <total-memory-limit> <image> <command>
		Ví dụ: docker run --memory 512M ubuntu bash
	+ Giới hạn CPU:
	Tương đối:
	docker run --cpu-shares=<limit> <image> <command>
	Ví dụ: docker run --cpu-shares=20 ubuntu bash
	Thời lượng và hạn mức:
	docker run --cpu-period=40000 --cpu-quota=20000 ubuntu bash
	=> nếu 1 CPU, thì container chiếm 50% CPU cứ mỗi 40ms

Một số lưu ý khi thao tác với container:
+ Hạn chế việc để container lấy dependency khi khởi động container. Nếu bạn sử dụng nodejs và để các dependency trong file package.json. Khi container chạy, nó sẽ tự động lấy về các dependency, rồi đến một lúc nào đó, bạn xoá đi một số package trong đó thì container của bạn cũng dừng luôn vì không thể lấy container về được vậy nên hãy đảm bảo rằng container lấy dependency từ ngay trong container chứ không lấy từ trên mạng
+ Không nên để những file quan trọng trong một container không tên và đã bị dừng. Vì có thể bạn sẽ vô tình xoá đi một vài container đã dừng để tiết kiệm ổ cứng hoặc dọn rác máy tính. Và trong đó cũng có container chứa những file quan trọng. Vì vậy nên đặt tên cho một container và lưu lại những file quan trọng một cách cẩn thận.


+ Kết nối mạng trong container
Mạng nội bộ trong container:
+ Các chương trình trong container được mặc định tác biệt hoàn toàn khỏi Internet
+ Nhóm các container lại thành một mạng nội bộ riêng và mỗi mạng thì chuyên về một lĩnh vực nào đó. Làm thế sẽ tránh được việc một container không liên quan sẽ can thiệp vào việc của một container khác
+ Kiểm soát được sự liên kết giữa container này với container kia
+ Sử dụng phương thức expose cổng và link container
+ Docker có cơ chế đặc biệt giúp các container có thể tìm và liên kết với nhau
+ Xác định cổng bên trong và bên ngoài
+ Không giới hạn số lượng cổng để mở

Mở cổng container:
+ Cần sự phối hợp giữa các container
+ Dễ dàng tìm kiếm các cổng được mở
+ Cổng bên trong container là cố định
+ Cổng bên ngoài container được lựa chọn ngẫu nhiên và docker sẽ tự động tìm cổng không bị conflict để kết nối. Docker đảm bảo sẽ luôn có cổng bên ngoài để kết nối
+ Cho phép nhiều container chạy các chương trình với cổng cố định
+ Thường được sử dụng với chương trình tìm kiếm dịch vụ (service discovery)

Mặc định nếu bạn chọn số cổng thì docker sẽ tự coi đó là cổng TCP, nếu bạn muốn chạy trên cổng UDP thì chạy lệnh sau:
docker run -p outside-port:inside-port/protocol (tcp/udp)
Ví dụ: docker run -p 111:222/udp
Thứ tự cổng là từ ngoài vào trong, xác định cổng bên ngoài container trước rồi đến cổng bên trong container


Liên kết giữa các container (xem bài 19)

Sử dụng lệnh "df -h" để hiển thị dung lượng các ổ đĩa, dung lượng đang sử dụng và dung lượng đang trống

Docker registry:
+ Docker registry quản lý và phân phối image
+ Dịch vụ miễn phí từ Docker
+ Có thể setup một Registry cho cá nhân hoặc doanh nghiệp