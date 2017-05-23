<p><a href="#1">1. Ảo hóa KVM/QEMU</a></p>
<ul>
<li>
<p><a href="#1.1">1.1. Giới thiệu</a></p>
</li>
<li>
<p><a href="#1.2">1.2. Các tính năng của ảo hóa KVM</a></p>
</li>
<li>
<p><a href="#1.3">1.3. Cấu trúc ảo hóa KVM kết hợp QEMU</a></p>
</li>
</ul>
<p><a href="#2">2. Cài đặt KVM</a></p>
<ul>
<li>
<p><a href="#2.1">2.1. Kiểm tra cấu hình hệ thống và cài đặt</a></p>
</li>
<li>
<p><a href="#2.2">2.2. Cấu hình</a></p>
</li>
</ul>
<p><a href="#3">3. Quản lý KVM bằng libvirt</a></p>
<ul>
<li>
<p><a href="#3.1">3.1. Giới thiệu</a></p>
</li>
<li>
<p><a href="#3.2">3.2. Các chức năng chính</a></p>
</li>
<li>
<p><a href="#3.3">Một số vấn đề cần biết với libvirt</a></p>
</li>
</ul>
<p><a href="#4">4. Tham khảo</a></p>
<hr>
<p><a name="user-content-1"></a></p>
<h1><a id="user-content-1-Ảo-hóa-kvmqemu" class="anchor" href="#1-Ảo-hóa-kvmqemu" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1. Ảo hóa KVM/QEMU</h1>
<p><a name="user-content-1.1"></a></p>
<h2><a id="user-content-11-giới-thiệu" class="anchor" href="#11-giới-thiệu" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.1. Giới thiệu</h2>
<p><strong>KVM</strong> ra đời phiên bản đầu tiên vào năm 2007 bởi công ty Qumranet tại Isarel, <em><strong>KVM được tích hợp sẵn vào nhân của hệ điều hành Linux</strong></em> bắt đầu từ phiên bản 2.6.20. Năm 2008, RedHat đã mua lại Qumranet và bắt đầu phát triển, phổ biến KVM Hypervisor.</p>
<p><em><strong>KVM (Kernel-based virtual machine)</strong></em> là giải pháp ảo hóa cho hệ thống linux trên nền tảng phần cứng x86 có các module mở rộng hỗ trợ ảo hóa (Intel VT-x hoặc AMD-V).</p>
<ul>
<li>
<p>Về bản chất, KVM không thực sự là một hypervisor có chức năng giải lập phần cứng để chạy các máy ảo. Chính xác KVM chỉ là một <strong>module của kernel linux</strong> hỗ trợ <strong>cơ chế mapping các chỉ dẫn trên CPU ảo (của guest VM) sang chỉ dẫn trên CPU vật lý (của máy chủ chứa VM)</strong>. Hoặc có thể hình dung KVM giống như một driver cho hypervisor để sử dụng được tính năng ảo hóa của các vi xử lý như Intel VT-x hay AMD-V, mục tiêu là tăng hiệu suất cho guest VM.</p>
</li>
<li>
<p>KVM hiện nay được thiết kế để giao tiếp với các hạt nhân thông qua một kernel module có thể nạp được. Hỗ trợ một loạt các hệ thống điều hành máy  guest như: Linux, BSD, Solaris, Windows, Haiku, ReactOS và  hệ điều hành  nghiên cứu AROS. Sử dụng  kết  hợp  với QEMU, KVM có thể chạy Mac OS X.</p>
</li>
<li>
<p>Trong kiến trúc của KVM, Virtual machine được thực hiện như là quy trình xử lý thông thường của Linux, được lập lịch hoạt động như các scheduler tiểu chuẩn của Linux. Trên thực tế, mỗi CPU ảo hoạt động như một tiến trình xử lý của Linux. Điều này cho phép KVM được hưởng lợi từ tất cả các tính năng của nhân Linux.</p>
</li>
</ul>
<p><a href="https://camo.githubusercontent.com/8943edb62d927a7d6643570fedc3ce027818002e/687474703a2f2f696d6775722e636f6d2f4a65764a6774372e6a7067" target="_blank"><img src="https://camo.githubusercontent.com/8943edb62d927a7d6643570fedc3ce027818002e/687474703a2f2f696d6775722e636f6d2f4a65764a6774372e6a7067" data-canonical-src="http://imgur.com/JevJgt7.jpg" style="max-width:100%;"></a></p>
<p>Kiến trúc KVM</p>
<p><a name="user-content-1.2"></a></p>
<h2><a id="user-content-12-các-tính-năng-của-ảo-hóa-kvm" class="anchor" href="#12-các-tính-năng-của-ảo-hóa-kvm" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2. Các tính năng của ảo hóa KVM</h2>
<h3><a id="user-content-121security--bảo-mật" class="anchor" href="#121security--bảo-mật" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2.1.	Security – Bảo mật</h3>
<p>-	Vì  Virtual machine được coi như một tiến trình xử lý của Linux, nên nó tận dụng được mô hình bảo mật tiêu chuẩn của Linux để cung cấp khả năng điều khiển và cô lập tài nguyên. Nhân Linux sử dụng SELinux (Security-Enhanced Linux) để thêm quyền điều khiển truy cập, bảo mật đa mức và bảo mật đa tiêu chí, và thực thi các chính  sách bắt buộc. SELinux cung cấp cơ chế cách ly tài nguyên nghiêm ngặt và hạn chế cho các tiến trình chạy trong nhân Linux.</p>
<p>-	Ngoài ra, dự án Svirt tích hợp bảo mật Mandatory Access Control (MAC) với KVM – xây dựng trên SELinux để cung cấp một nền tảng ảo hóa cho phép người quản trị định nghĩa các chính sách cho sự cô lập máy ảo. Svirt đảm bảo máy ảo không thể bị truy cập bởi các tiến trình khác (hoặc máy ảo khác), việc này có thể được mở rộng thêm bởi người quản trị hệ thống định nghĩa ra các quyền hạn đặc biệt: như là nhóm các máy ảo chia sẻ chung nguồn tài nguyên.</p>
<h3><a id="user-content-122memory-management--quản-lý-bộ-nhớ" class="anchor" href="#122memory-management--quản-lý-bộ-nhớ" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2.2.	Memory management – Quản lý bộ nhớ</h3>
<p>-	KVM thừa kế tính năng quản lý bộ nhớ mạnh mẽ của Linux. Vùng nhớ của máy ảo được lưu trữ trên cùng một vùng nhớ dành cho các tiến trình Linux khác và có thể swap. KVM hỗ trợ NUMA (Non-Uniform Memory Access - bộ nhớ thiết kế cho hệ thống đa xử lý) cho phép tận dụng hiệu quả vùng nhớ kích thước lớn.</p>
<p>-	KVM hỗ trợ các tính năng ảo của mới nhất từ các nhà cung cấp CPU như EPT (Extended Page Table) của Microsoft, Rapid Virtualization Indexing (RVI) của AMD để giảm thiểu mức độ sử dụng CPU và cho thông lượng cao hơn.</p>
<p>-	Việc chia sẻ bộ nhớ được hỗ trợ thông qua một tính năng của nhân gọi là Kernel Same-page Merging (KSM). KSM quét tất cả các yêu cầu về vùng nhớ cần thiết cho máy ảo và định danh cho từng máy ảo, sau đó tổ hợp vào thành một yêu cầu về vùng nhớ duy nhất để chia sẻ chung cho các máy ảo, lưu trữ vào một bản copy.</p>
<h3><a id="user-content-123storage--lưu-trữ" class="anchor" href="#123storage--lưu-trữ" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2.3.	Storage – Lưu trữ</h3>
<p>-	KVM sử dụng khả năng lưu trữ hỗ trợ bởi Linux để lưu trữ các images máy ảo, bao gồm các local dish với chuẩn IDE, SCSI và SATA, Network Attached Storage (NAS) bao gồm NFS và SAMBA/CIFS, hoặc SAN được hỗ trợ iSCSI và Fibre Chanel.</p>
<p>-	Một lần nữa, bởi vì KVM là một phần trong nhân Linux, nên nó có thể tận dụng hạ tầng lưu trữ đã được chứng minh và đáng tin cậy với sự hỗ trợ từ tất cả các nhà cung cấp hàng đầu trong lĩnh vực Storage.</p>
<p>-	KVM cũng hỗ trợ các image của máy ảo trên hệ thống chia sẻ tập tin như GFS2 cho phép các image máy ảo được chia sẻ giữa các host hoặc các logical volumn.</p>
<p>-	Định dạng image tự nhiên của KVM là QCOW2 – hỗ trợ việc snapshot cho phép snapshot nhiều mức, nén và mã hóa dữ liệu.</p>
<h3><a id="user-content-124live-migration" class="anchor" href="#124live-migration" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2.4.	Live migration</h3>
<p>-	KVM hỗ trợ live migration cung cấp khả năng di chuyển ác máy ảo đang chạy giữa các host vật lý mà không làm gián đoạn dịch vụ. Khả năng live migration là trong suốt với người dùng, các máy ảo vẫn duy trì trạng thái bật, kết nối mạng vẫn đảm bảo và các ứng dụng của người dùng vẫn tiếp tục duy trì trong khi máy ảo được đưa sang một host vật lý mới.</p>
<p>-	KVM cũng cho phép lưu lại trạng thái hiện tại của máy ảo để cho phép lưu trữ và khôi phục trạng thái đó vào lần sử dụng tiếp theo.</p>
<h3><a id="user-content-125performance-and-scalability" class="anchor" href="#125performance-and-scalability" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.2.5.	Performance and scalability</h3>
<p>KVM kế thừa hiệu năng và khả năng mở rộng của Linux, hỗ trợ các máy ảo với 16 CPUs ảo, 256GB RAM và hệ thống máy chủ lên tới 256 cores và trên 1TB RAM.</p>
<p><a name="user-content-1.3"></a></p>
<h2><a id="user-content-13-cấu-trúc-ảo-hóa-kvm-kết-hợp-qemu" class="anchor" href="#13-cấu-trúc-ảo-hóa-kvm-kết-hợp-qemu" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>1.3. Cấu trúc ảo hóa KVM kết hợp QEMU</h2>
<p>Hình sau mô tả cấu trúc KVM stack làm việc chung với QEMU:</p>
<p><a href="https://camo.githubusercontent.com/35ed680bfee92e09ae2377055f0cddcef96921b5/687474703a2f2f696d6775722e636f6d2f777341356846372e6a7067" target="_blank"><img src="https://camo.githubusercontent.com/35ed680bfee92e09ae2377055f0cddcef96921b5/687474703a2f2f696d6775722e636f6d2f777341356846372e6a7067" data-canonical-src="http://imgur.com/wsA5hF7.jpg" style="max-width:100%;"></a></p>
<p>Trong đó:</p>
<p>-	<strong>User-facing tools</strong>: Là các công cụ quản lý máy ảo hỗ trợ KVM. Các công cụ có giao diện đồ họa (như virt-manager) hoặc giao diện dòng lệnh như (virsh) và virt-tool (Các công cụ này được quản lý bởi thư viện libvirt).</p>
<p>Các công cụ quản lý KVM do libivrt cung cấp tham khảo thêm tại: <a href="http://www.linux-kvm.org/page/Management_Tools">http://www.linux-kvm.org/page/Management_Tools</a></p>
<p>-	<strong>Management layer</strong>: Lớp này là thư viện libvirt cung cấp API để các công cụ quản lý máy ảo hoặc các hypervisor tương tác với KVM thực hiện các thao tác quản lý tài nguyên ảo hóa, bởi vì KVM chỉ là một module của nhân hỗ trợ cơ chế mapping các chỉ dẫn của CPU ảo để thực hiện trên CPU thật, nên tự thân KVM không hề có khả năng giả lập và quản lý tài nguyên ảo hóa. Mà phải dùng nhờ các công nghệ hypervisor khác, thường là QEMU.</p>
<p>-	<strong>Virtual machine</strong>: Chính là các máy ảo người dùng tạo ra. Thông thường, nếu không sử dụng các công cụ như virsh hay virt-manager, KVM sẽ sử được sử dụng phối hợp với một hypervisor khác điển hình là QEMU.</p>
<p>-	<strong>Kernel support</strong>: Chính là KVM, cung cấp một module làm hạt nhân cho hạ tầng ảo hóa (kvm.ko) và một module kernel đặc biệt chỉ hỗ trợ các vi xử lý VT-x hoặc AMD-V (kvm-intel.ko hoặc kvm-amd.ko) để nâng cao hiệu suất ảo hóa.</p>
<p><em><strong>Một số lưu ý về KVM và QEMU</strong></em>:</p>
<ul>
<li>
<p><em>Có thể hình dung KVM giống như driver cho hypervisor để sử dụng được virtualization extension của physical CPU nhằm boost performance cho guest VM. KVM như định nghĩa trên trang chủ thì là core virtualization infrastructure mà thôi, nó được các hypervisor khác lợi dụng làm back-end để tiếp cận được các công nghệ hardware acceleration (Dịch code để mô phỏng phần cứng)</em></p>
</li>
<li>
<p><em>QEMU là một Emulator nên nó có bộ dịch của nó là TCG (Tiny Code Generate) để xử lý các yêu cầu trên CPU ảo và giả lập kiến trúc của máy ảo. Nên có thể coi, QEMU như là một hypervisor type 2 (hypervisor chỉ chung cho chức năng ảo hóa). Nhằm nâng cao hiệu suất của VM. Cụ thể, lúc tạo VM bằng QEMU có VirtType là KVM thì khi đó các instruction có nghĩa đối với virtual CPU sẽ được QEMU sử dụng KVM để mapping thành các instruction có nghĩa đối với physical CPU. Làm như vậy sẽ nhanh hơn là chỉ chạy độc lập QEMU, vì nếu không có KVM thì QEMU sẽ phải quay về (fall-back) sử dụng translator của riêng nó là TCG để chuyển dịch các instruction của virtual CPU rồi đem thực thi trên physical CPU.</em></p>
</li>
</ul>
<p>=&gt; Khi QEMU/KVM kết hợp nhau thì tạo thành type-1 hypervisor.</p>
<ul>
<li>
<p><em><strong>QEMU cần KVM để boost performance và ngược lại KVM cần QEMU (modified version) để cung cấp giải pháp full virtualization hoàn chỉnh.</strong></em></p>
</li>
<li>
<p><em><strong>Do KVM kết hợp QEMU nên các máy ảo và mạng ảo có file cấu hình xml sẽ được lưu lại tại thư mục <code>/etc/libvirt/qemu/</code></strong></em></p>
</li>
</ul>
<p><a name="user-content-2"></a></p>
<h1><a id="user-content-2-cài-đặt-kvm" class="anchor" href="#2-cài-đặt-kvm" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>2. Cài đặt KVM</h1>
<p><a name="user-content-2.1"></a></p>
<h2><a id="user-content-21-kiểm-tra-hệ-thống-và-cài-đặt" class="anchor" href="#21-kiểm-tra-hệ-thống-và-cài-đặt" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>2.1. Kiểm tra hệ thống và cài đặt</h2>
<p>KVM chỉ làm việc trên hệ thống mà CPU có hỗ trợ ảo hóa phần cứng mở rộng – Intel VT-x hoặc AMD-V. Để kiểm tra xem CPU có hỗ trợ tính năng ảo hóa mở rộng này hay không, gõ dòng lệnh sau:</p>
<p><code>egrep -c ‘(svm|vmx)’ /proc/cpuinfo</code></p>
<p>Nếu kết quả đầu ra là 0 thì CPU không hỗ trợ, còn nếu đầu ra lớn hơn 0 thì có nghĩa là CPU có hỗ trợ ảo hóa sử dụng KVM.</p>
<p>Nếu đang sử dụng các máy ảo trong Vmware để tìm hiểu về hệ thống Linux, bạn phải lựa chọn tùy chọn hỗ trợ ảo hóa phần cứng mở rộng như sau:</p>
<p><a href="https://camo.githubusercontent.com/8d9c335e9a97658a540d187dde7c081f76fa9c5f/687474703a2f2f696d6775722e636f6d2f6d666a42476e442e6a7067" target="_blank"><img src="https://camo.githubusercontent.com/8d9c335e9a97658a540d187dde7c081f76fa9c5f/687474703a2f2f696d6775722e636f6d2f6d666a42476e442e6a7067" data-canonical-src="http://imgur.com/mfjBGnD.jpg" style="max-width:100%;"></a></p>
<ul>
<li>Kiểm tra hệ thống xem có hỗ trợ KVM trong kernel hay không sử dụng lệnh:</li>
</ul>
<p><code>lsmod | grep kvm</code></p>
<p>Kết quả trả về như sau:</p>
<p><a href="https://camo.githubusercontent.com/44be1cf604777621fe72e2451d57a4a8e6477652/687474703a2f2f696d6775722e636f6d2f47634474684f522e6a7067" target="_blank"><img src="https://camo.githubusercontent.com/44be1cf604777621fe72e2451d57a4a8e6477652/687474703a2f2f696d6775722e636f6d2f47634474684f522e6a7067" data-canonical-src="http://imgur.com/GcDthOR.jpg" style="max-width:100%;"></a></p>
<ul>
<li>Sau khi đã kiểm tra hệ thống có hỗ trợ ảo hóa KVM, ta tiến hành cài đặt các gói phần mềm sau để hỗ trợ ảo hóa và quản lý các máy ảo KVM/QEMU:</li>
</ul>
<p><code>sudo apt-get update</code></p>
<p><code>sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager -y</code></p>
<p><a name="user-content-2.2"></a></p>
<h2><a id="user-content-22cấu-hình" class="anchor" href="#22cấu-hình" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>2.2.	Cấu hình</h2>
<p>Sau khi cài đặt KVM và các gói phần mềm cần thiết hỗ trợ quản lý máy ảo thành công. Bây giờ sẽ tiến hành cấu hình.</p>
<p>Ban đầu, chỉ có người dùng root và các người dùng trong nhóm người dùng libvirtd (nhóm người dùng có thể sử dụng các API trong thư viện quản lý ảo hóa libvirt) mới có quyền sử dụng các máy ảo KVM. Sử dụng lệnh sau để add thêm người dùng mà bạn muốn sử dụng để có thể quản lý các máy ảo KVM bằng lệnh sau:</p>
<p><code>sudo adduser &lt;user_name&gt; libvirtd</code></p>
<p>Sau khi chạy xong lệnh sau, đăng xuất và đăng nhập lại, chạy lệnh tiếp theo được kết quả như sau là mọi thứ đã làm việc tốt:</p>
<p><code>virsh -c qemu:///system list</code></p>
<p><a href="https://camo.githubusercontent.com/64a7e90d9e4abdeb4447d93d2fb79c6a8b27eb32/687474703a2f2f696d6775722e636f6d2f43524d774d51552e6a7067" target="_blank"><img src="https://camo.githubusercontent.com/64a7e90d9e4abdeb4447d93d2fb79c6a8b27eb32/687474703a2f2f696d6775722e636f6d2f43524d774d51552e6a7067" data-canonical-src="http://imgur.com/CRMwMQU.jpg" style="max-width:100%;"></a></p>
<p><a name="user-content-3"></a></p>
<h1><a id="user-content-3-quản-lý-kvm-bằng-libvirt" class="anchor" href="#3-quản-lý-kvm-bằng-libvirt" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>3. Quản lý KVM bằng libvirt</h1>
<p><a name="user-content-3.1"></a></p>
<h2><a id="user-content-31-giới-thiệu" class="anchor" href="#31-giới-thiệu" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>3.1. Giới thiệu</h2>
<ul>
<li>
<p><strong>Libvirt</strong> là một bộ các phần mềm mà cung cấp các cách thuận tiện để quản lý máy ảo và các chức năng của ảo hóa, như là chức năng quản lý lưu trữ và giao diện mạng. Những phần mềm này bao gồm một thư viện API, daemon (libvirtd) và các gói tiện tích giao diện dòng lệnh (virsh).</p>
</li>
<li>
<p>Mục đích chính của Libvirt là cung cấp một cách duy nhất để quản lý ảo hóa từ các nhà cung cấp và các loại hypervisor khác nhau. Ví dụ, dòng lệnh <code>virsh list –all</code> có thể được sử dụng để liệt kê ra các máy ảo đang tồn tại cho một số hypervisor được hỗ trợ (KVM, Xen, Vmware ESX, … ). Không cần thiết phải học một tool xác định cho từng hypervisor.</p>
</li>
</ul>
<p><a name="user-content-3.2"></a></p>
<h2><a id="user-content-32-các-chức-năng-chính" class="anchor" href="#32-các-chức-năng-chính" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>3.2. Các chức năng chính</h2>
<ul>
<li>
<p><strong>VM management – Quản lý máy ảo</strong>: Quản lý vòng đời các domain như là start, stop, pause, save, restore và migrate. Các hoạt động hotplug cho nhiều loại thiết bị bao gồm disk và network interfaces, memory, và cpus.</p>
</li>
<li>
<p><strong>Remote machine support</strong>: Tất cả các chức năng của libvirt có thể được truy cập trên nhiều máy chạy libvirt deamon, bao gồm cả các remote machine. Hỗ trợ kết nối từ xa, với cách đơn giản nhất là dùng SSH – không yêu cầu cấu hình thêm gì thêm. Nếu example.com đang chạy libvirtd và truy cập SSH được cho phép, câu lệnh sau sẽ cung cấp khả năng truy cập tới tất cả câu lệnh virsh trên remote host cho các máy ảo qemu/kvm:</p>
</li>
</ul>
<p><code>virsh --connect qemu+ssh://root@example.com/system</code></p>
<p>Tham khảo thêm tại: <a href="https://libvirt.org/remote.html">https://libvirt.org/remote.html</a></p>
<ul>
<li>
<p><strong>Storage management</strong>: bất kì host nào đang chạy libvirt daemon có thể được sử dụng để quản lý nhiều loại storage: tạo file image với nhiều định dạng phong phú (qcow2, vmdk, raw, …), mount NFS shares, liệt kê các nhóm phân vùng LVM, tạo nhóm phân cùng LVM mới, phân vùng ổ cứng, mount iCSI shares, và nhiều hơn nữa. vì libvirt làm tốt việc truy cập từ xa nên những tùy chọn này là có sẵn trên remote host.  (Xem thêm tại : <a href="http://libvirt.org/storage.html">http://libvirt.org/storage.html</a> )</p>
</li>
<li>
<p><strong>Network interface management:</strong> bất kì host nào chạy libvirt daemon có thể được sử dụng để quản lý các interface netowork vật lý và logic. Liệt kê các interface đang tồn tại, cũng như là cấu hình (hoặc tạo, xóa) các interfaces, bridge, vlans, và bond devices.</p>
</li>
<li>
<p><strong>Virtual NAT and Route based networking:</strong> Quản lý và tạo các mạng ảo, Libvirt virtual network sử dụng firewall để hoạt động như là router, cung cấp các máy ảo trong suốt truy cập tới mạng của host. (xem thêm tại: <a href="http://libvirt.org/archnetwork.html">http://libvirt.org/archnetwork.html</a> )</p>
</li>
</ul>
<p><a name="user-content-3.3"></a></p>
<h2><a id="user-content-33-một-số-vấn-đề-cần-biết-với-libvirt" class="anchor" href="#33-một-số-vấn-đề-cần-biết-với-libvirt" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>3.3. Một số vấn đề cần biết với libvirt</h2>
<p>Libvirt được cấu hình để lưu trữ các máy ảo QEMU và các file cấu hình XML trong thư mục <code>/etc</code>, nhưng việc sửa những file này thì không phải là cách để thay đổi thông tin cấu hình. Nếu sửa những file này và khởi động lại libvirtd có thể làm việc trong vài lần, có thể libvirtd sẽ ghi đè những thay đổi này và chúng sẽ bị mất. Quan trọng là nên dùng các công cụ của virsh hoặc các API khác để sửa file XML cho phép libvirt xác nhận những thay đổi của bạn.</p>
<p>Sau đây là một số câu lệnh kết hợp với virsh áp dụng cho tất cả các loại XML của libvirt:</p>
<p>-	Virtual Networks: net-edit, net-dumpxml, net-start, …</p>
<p>-	Storage Pools: pool-edit, pool-dumpxml, pool-define, ….</p>
<p>-	Storage Volumes: vol-edit, vol-dumpxml, vol-define, ….</p>
<p>-	Interfaces: iface-edit, iface-dumpxml, iface-start, ….</p>
<p>Tham khảo thêm tại <a href="http://manpages.ubuntu.com/manpages/xenial/man1/virsh.1.html"> virsh command</a>.</p>
<p><a name="user-content-4"></a></p>
<h1><a id="user-content-4-tham-khảo" class="anchor" href="#4-tham-khảo" aria-hidden="true"><svg aria-hidden="true" class="octicon octicon-link" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>4. Tham khảo</h1>
<p>[1] <a href="https://github.com/thaihust/Thuc-tap-thang-03-2016/blob/master/ThaiPH/KVM/ThaiPH_WebVirt_lab.md">https://github.com/thaihust/Thuc-tap-thang-03-2016/blob/master/ThaiPH/KVM/ThaiPH_WebVirt_lab.md</a></p>
<p>[2] <a href="http://www.innervoice.in/blogs/2014/03/10/kvm-and-qemu/">http://www.innervoice.in/blogs/2014/03/10/kvm-and-qemu/</a></p>
<p>[3] <a href="http://blogit.edu.vn/gioi-thieu-ao-hoa-va-ao-hoa-ma-nguon-kvm-hypervisor/">http://blogit.edu.vn/gioi-thieu-ao-hoa-va-ao-hoa-ma-nguon-kvm-hypervisor/</a></p>
<p>[4] <a href="https://www.ibm.com/developerworks/library/l-using-kvm/">https://www.ibm.com/developerworks/library/l-using-kvm/</a></p>
<p>[5] <a href="https://manthang.wordpress.com/2014/06/18/kvm-qemu-do-you-know-the-connection-between-them/">https://manthang.wordpress.com/2014/06/18/kvm-qemu-do-you-know-the-connection-between-them/</a></p>
<p>[6] Cài đặt: <a href="https://www.howtogeek.com/117635/how-to-install-kvm-and-create-virtual-machines-on-ubuntu/">https://www.howtogeek.com/117635/how-to-install-kvm-and-create-virtual-machines-on-ubuntu/</a></p>
