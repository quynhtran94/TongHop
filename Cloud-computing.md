<h5>CLOUD COMPUTING</h5>
Là mô hình cho phép truy cập qua mạng để lựa chọn và sử dụng tài nguyên  có thể được tính toán (ví dụ: mạng, máy chủ, lưu trữ, ứng dụng và dịch vụ) theo nhu cầu một cách thuận tiện và nhanh chóng; đồng thời cho phép kết thúc sử dụng dịch vụ, giải phóng tài nguyên dễ dàng, giảm thiểu các giao tiếp với nhà cung cấp.
<p>
<p><em><strong>5 đặc điểm: (giải thích chi tiết sẽ có bài viết cụ thể hơn)</strong></em></p>
<ul>
<li>Khả năng thu hồi và cấp phát tài nguyên (Rapid elasticity)</li>
<li>Truy nhập qua các chuẩn mạng (Broad network access)</li>
<li>Dịch vụ sử dụng đo đếm được (Measured service,) hay là chi trả theo mức độ sử dụng pay as you go.</li>
<li>Khả năng tự phục vụ (On-demand self-service).</li>
<li>Chia sẻ tài nguyên (Resource pooling).</li>
</ul>
<p><em><strong>4 mô hình dịch vụ (mô hình sản phẩm): cũng sẽ giải thích chi tiết hơn trong bài viết cụ thể</strong></em></p>
<ul>
<li>Public Cloud: Đám mây công cộng (là các dịch vụ trên nền tảng Cloud Computing để cho các cá nhân và tổ chức thuê, họ dùng chung tài nguyên).</li>
<li>Private Cloud: Đám mây riêng (dùng trong một doanh nghiệp và không chia sẻ với người dùng ngoài doanh nghiệp đó)</li>
<li>Community Cloud: Đám mây cộng đồng (là các dịch vụ trên nền tảng Cloud computing do các công ty cùng hợp tác xây dựng và cung cấp các dịch vụ cho cộng đồng. Tôi cũng chưa rõ FB có phải là một dạng này không, cần xác nhận lại.</li>
<li>Hybrid Cloud : Là mô hình kết hợp (lai) giữa các mô hình Public Cloud và Private Cloud (không rõ có Community Cloud nữa không &#8230; :D)</li>
</ul>
<p><strong><em>3 mô hình triển khai: tức là triển khai Cloud Computing để cung cấp:</em></strong></p>
<ul>
<li>Hạ tầng như một dịch vụ (Infrastructure as a Service)</li>
<li>Nền tảng như một dịch vụ (Platform as a Service)</li>
<li>Phần mềm như một dịch vụ (Software as a Service)</li>
</ul>

<p>
<h3> VIRTUALIZATION </h3>
<h4> ẢO HÓA PHẦN CỨNG</h4>
<ul>
<li><p><strong>Full Virtualization</strong>
Sự mô phỏng gần như hoàn toàn của phần cứng thực cho phép phần mềm (guest OS) chạy mà không có thay đổi.</p></li>
<li><p><strong> Partial virtualization </strong>
Ảo hóa cục bộ :  Một vài nhưng không phải tất cả môi trường mục tiêu được mô phỏng. Một vài chương trình guest, thành ra, có thể cần sự thay đổi để chạy trong môi trường ảo này.</p></li>
<li><p><strong> Paravirtualization </strong>
Ảo hóa song song: Môi trường phần cứng không được mô phỏng; tuy nhiên, chương trình guest được thực thi trên miền cách ly của chúng, giống như chúng đang chạy trên một hệ thống khác biệt. Chương trình guest cần sự thay đổi một cách đặc biệt để chạy trong môi trường này.</p></li>
</ul>

<ul>
<li><p><strong>Full Virtualization</strong> is virtualization in which the guest operating system is unaware that it is in a virtualized environment, and therefore hardware is virtualized by the host operating system so that the guest can issue commands to what it thinks is actual hardware, but really are just simulated hardware devices created by the host. </p></li>
<li><p><strong>Paravirtualization</strong> is virtualization in which the guest operating system (the one being virtualized) is aware that it is a guest and accordingly has drivers that, instead of issuing hardware commands, simply issue commands directly to the host operating system. This also includes  memory and thread management as well, which usually require unavailable privileged instructions in the processor.</p></li>
<li><p><strong>Hardware Assisted Virtualization</strong> is a type of Full Virtualization where the microprocessor architecture has special instructions to aid the virtualization of hardware. These instructions might allow a virtual context to be setup so that the guest can execute privileged instructions directly on the processor without affecting the host. Such a feature set is often called a <strong>Hypervisor</strong>. If said instructions do not exist, Full Virtualization is still possible, however it must be done via software techniques such as <strong>Dynamic Recompilation</strong> where the host recompiles on the fly privileged instructions in the guest to be able to run in a non-privileged way on the host.</p></li>
<li><p>There is also a combination of Para Virtualization and Full Virtualization called <strong>Hybrid Virtualization</strong> where parts of the guest operating system use paravirtualization for certain hardware drivers, and the host uses full virtualization for other features. This often produces superior performance on the guest without the need for the guest to be completely paravirtualized. An example of this: The guest uses full virtualization for privileged instructions in the kernel but paravirtualization for IO requests using a special driver in the guest. This way the guest operating system does not need to be fully paravirtualized, since this is sometimes not available, but can still enjoy some paravirtualized features by implementing special drivers for the guest.</p></li>
</ul>
<p>
<h4> ẢO HÓA PHẦN MỀM</h4>
<h3> THUẬT NGỮ </h3>
<h5> HOST MACHINE </h5>
Máy thật nơi đang triển khai ảo hóa trên đó.
<h5> GUEST MACHINE </h5>
Các máy ảo.
<h5> HYPERVISOR hay VM Monitor </h5>
Phần mềm hay Firmware đã tạo ra máy ảo trên phần cứng máy chủ.
Mô phỏng phần cứng, cấp phát, quản lý các tài nguyên, các máy ảo.
