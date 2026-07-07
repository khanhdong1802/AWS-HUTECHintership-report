---
title: "Event 1"
date: 2026-05-09
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Bài thu hoạch “FCAJ Community Day”

### Mục Đích Của Sự Kiện

- Chia sẻ các phương pháp khoa học để tạo động lực và duy trì kỷ luật trong tự học (quản lý Dopamine).
- Hướng dẫn kỹ thuật viết Prompt nâng cao (Ultimate Prompt Engineering) để tối ưu hóa hiệu suất làm việc với LLM.
- Định hướng tư duy, lộ trình nghề nghiệp và tiêu chí tuyển dụng thực tế dành cho sinh viên CNTT năm 3 và năm 4.
- Giới thiệu phương pháp phát triển phần mềm BMX (BMM method) để ứng dụng AI Agent hiệu quả và giảm thiểu lỗi ảo tưởng (hallucination).

### Danh Sách Diễn Giả

- **Anh Long** - Chuyên gia chia sẻ về Kỹ thuật Hack não bộ
- **Anh Thịnh** -Ultimate Prompt Engineering.
- **Anh Khang** - Solutions Architect tại Cloud Kinetics (Hơn 3 năm kinh nghiệm tuyển dụng và định hướng kỹ sư công nghệ).
- **Chị Thảo** - Software Developer tại Ngân hàng Quốc tế Việt Nam (VIB).

### Nội Dung Nổi Bật

#### Phương pháp "Hack não bộ" để nghiện học như nghiện Game/Mạng xã hội

- **Cơ chế Dopamine**: Dopamine không sinh ra khi nhận phần thưởng, mà tiết ra mạnh nhất khi não bộ _kỳ vọng phần thưởng sắp đến_. Bản chất thuật toán của các mạng xã hội (như TikTok) là giữ chân người dùng bằng tính tò mò (không biết video tiếp theo là gì).
- **Áp dụng vào học tập qua 4 cơ chế**: Tò mò → Thử nghiệm → Khám phá → Thỏa mãn.
- **Các kỹ thuật cốt lõi**:
- _Tâm lý sợ mất chuỗi (Loss Aversion)_: Sử dụng lịch vật lý hoặc ứng dụng để tích điểm chuỗi ngày học liên tục (giống như lập chuỗi Duolingo/TikTok). Tâm lý tiếc nuối một chuỗi ngày dài sẽ ép bản thân phải duy trì việc học dù chỉ 5–10 phút mỗi ngày.
- _Đánh lừa hạch hạnh nhân (Amygdala)_: Não bộ thường sợ hãi và trì hoãn trước khối lượng kiến thức khổng lồ. Hãy chia nhỏ mục tiêu (ví dụ: thay vì học AWS 5 tiếng, hãy học 1 dịch vụ trong 10-20 phút).
- _Quy tắc 2 phút_: Việc gì có thể hoàn thành trong 2 phút (như mở file bài tập, đọc một trang sách) thì phải làm ngay lập tức để phá vỡ sự trì hoãn.
- _Hệ thống Phản hồi & Gamification_: Tự thiết kế hệ thống tích điểm kinh nghiệm (XP), thăng hạng (Rank Đồng, Bạc, Vàng, Cam như hệ thống rank tại FCJ) và chuẩn bị các hộp bốc thăm phần thưởng ngẫu nhiên sau mỗi phiên học để kích thích Dopamine.

#### Kỹ thuật Kỹ nghệ Prompt Nâng cao (Ultimate Prompt Engineering)

- **7 thành phần của một Prompt chuẩn chỉnh**: Gồm có Role (Vai trò), Instruction (Chỉ thị), Context (Bối cảnh), Input Data (Dữ liệu đầu vào), Output Format (Định dạng đầu ra), Examples (Ví dụ minh họa), và Constraints (Ràng buộc).
- **Tối ưu hóa Token**: Token là các cụm từ phụ (subwords) mà LLM xử lý. Lưu ý rằng văn bản tiếng Việt thường tiêu tốn lượng token gấp đôi tiếng Anh khi qua bộ mã hóa (Tokener), làm tăng chi phí sử dụng API của doanh nghiệp.
- **Kỹ thuật tư duy nâng cao**:
- _Chain of Thought (CoT)_: Ép AI suy nghĩ từng bước một để tăng độ chính xác.
- _Tree of Thought (ToT)_: Mô hình cây tư duy kết hợp thuật toán tìm kiếm DFS/BFS để AI tự đánh giá và chọn ra hướng giải quyết tối ưu nhất.
- _Self-Consistency_: Chạy nhiều luồng suy nghĩ độc lập và lấy kết quả theo số đông.

- **Case Study (Project Prompt Optimizer)**: Dự án thực tế tối ưu hóa prompt tự động được chạy hoàn toàn trên kiến trúc Serverless của AWS bao gồm: CloudFront + S3 (Frontend/CDN), Amazon Cognito (Quản lý User/JWT), API Gateway + AWS Lambda (Backend điều phối traffic và xử lý logic), DynamoDB (Lưu lịch sử prompt với tốc độ phản hồi mili-giây) và Amazon Bedrock để kết nối an toàn với các Foundation Models như Claude 3.5 Sonnet.

#### Định hướng nghề nghiệp dành cho sinh viên ("Tại sao các bạn chưa đi làm?")

- **Hiệu ứng Khuếch đại của AI (AI Amplification)**: AI làm cho người lười/người tệ trở nên tệ hơn (copy-paste code rác mà không hiểu bản chất), nhưng giúp người giỏi nhân đôi, nhân mười hiệu suất. Nhà tuyển dụng sẵn sàng chọn một ứng viên 6-7 điểm tự tư duy hơn là bài nộp 9-10 điểm tạo từ AI nhưng hỏi lại không biết giải thích.
- **Tư duy "Why" thay vì "What"**: Thay vì chỉ quan tâm làm thế nào để xong bài tập (What), hãy luôn đặt câu hỏi "Tại sao lại chọn kiến trúc/dịch vụ này?", "Trade-off của nó là gì?". Hãy đặt câu hỏi "Why" ít nhất 5 lần để tìm ra gốc rễ vấn đề.
- **Sự liêm chính (Integrity) & Tầm nhìn dài hạn**: Khi nhận 10 yêu cầu từ dự án, người có sự liêm chính sẽ tự bóc tách ra 20-30 trường hợp ngoại lệ (Edge cases) để xử lý triệt để, làm việc có tâm ngay cả khi quản lý không giám sát. Hãy tập trung đầu tư trải nghiệm dài hạn thay vì chỉ nhìn vào mức lương ngắn hạn khi mới ra trường.
- **Mô hình 3 vòng tròn công việc & 5 lợi ích**: Công việc lý tưởng là điểm giao giữa: Việc bạn thích, Việc bạn bắt buộc phải làm theo chuẩn doanh nghiệp, và Lợi ích nhận được. 5 lợi ích của một công việc bao gồm: Lương (Salary), Kinh nghiệm (Experience), Mạng lưới quan hệ (Network), Kiến thức (Knowledge) và Cơ hội thăng tiến (Promotion).
- **Bộ 5 tiêu chí đánh giá ứng viên của doanh nghiệp**: Thái độ (Attitude - Tiêu chí số 1 đối với Fresher), Trình độ/Học vấn (Education), Kinh nghiệm (Experience), Trải nghiệm thực tế (Exposure), và Tố chất bản năng (Talents).

#### Phương pháp phát triển phần mềm BMX (BMM Method) kết hợp AI Agent

- **Khái niệm**: BMX (Through Method for Agile Development) là một phương pháp mã nguồn mở giúp chuẩn hóa quy trình làm việc với AI theo mô hình Agile truyền thống bằng cách chia nhỏ thành các vai trò AI Agent độc lập (PM, Architect, PO, Scrum Master, Developer, Reviewer/QA).
- **Giảm thiểu ảo tưởng (Hallucination)**: Khi nhồi nhét quá nhiều yêu cầu vào một khung chat, Context Window phình to khiến AI dễ bị loạn và sinh code lỗi. Phương pháp BMX giải quyết bằng cách cắt nhỏ tài liệu PRD (Product Requirement Document) và System Architecture thành các Epic và Story nhỏ gọn độc lập.
- **Cơ chế hoạt động**: Các Story được kiểm soát trạng thái chặt chẽ bởi con người (Draft → Approved → Review → Done). Developer Agent chỉ tự động nhận và code những Story đã được phê duyệt (Approved). Sau đó Reviewer Agent và QA Agent sẽ liên tục chạy vòng lặp kiểm thử cho đến khi tỷ lệ lỗi bằng 0. Triết lý cốt lõi: Bạn không cần quản lý mã nguồn, bạn chỉ cần viết Document thật chuẩn chỉnh, AI sẽ tự động sinh mã nguồn tốt nhất.

### Những Gì Học Được

#### Tư Duy Thiết Kế

- **Tư duy hướng bản chất (Foundation-first approach)**: Không cần chạy theo Master tất cả 200–300 dịch vụ của AWS, thay vào đó chỉ cần hiểu thật sâu và chắc chắn 5 dịch vụ nền tảng cốt lõi là đủ để phát triển kiến trúc.
- **Tư duy phản biện kiến trúc**: Sử dụng quy trình tư duy (Thought process) để liên tục đặt câu hỏi "Why" với AI, không chấp nhận các câu trả lời mặc định nhằm tránh bẫy ảo tưởng (hallucination) của mô hình ngôn ngữ lớn.

#### Kiến Trúc Kỹ Thuật

- Nắm vững **7 thành phần cấu trúc Prompt chuyên sâu** để áp dụng vào thực tế công việc hàng ngày, giúp tăng chất lượng phản hồi từ AI.
- Hiểu được chi phí vận hành hệ thống LLM thông qua **cơ chế tính Token**, đặc biệt là sự chênh lệch chi phí (gấp đôi) khi xử lý ngôn ngữ tiếng Việt so với tiếng Anh.
- Hiểu cách **phối hợp các AI Agent** theo mô hình BMX: Phân chia module, đóng gói Context Window để các Agent chuyên trách thực thi từng nhiệm vụ riêng biệt mà không làm ảnh hưởng đến các tính năng khác của hệ thống.

#### Chiến Lược Hiện Đại Hóa

- **Chiến lược phát triển dựa trên tài liệu (Document-driven)**: Chuyển dịch tư duy từ việc tập trung viết mã nguồn sang việc viết tài liệu kỹ thuật chuẩn chỉnh để tận dụng tối đa năng lực tự động sinh code của AI Agent.
- **Chiến lược tích lũy trải nghiệm dài hạn**: Sẵn sàng đón nhận sai lầm (Embrace mistakes) và nhìn nhận hành trình sự nghiệp một cách dài hạn (Long-term vision) thay vì chỉ tập trung vào các mục tiêu tài chính ngắn hạn trước mắt.

### Ứng Dụng Vào Công Việc

- **Tái cấu trúc quy trình Prompt hàng ngày**: Áp dụng đầy đủ 7 thành phần cấu trúc (Role, Instruction, Context, Constraints...) vào workflow để nâng cao hiệu suất làm việc với Gemini/ChatGPT.
- **Áp dụng tư duy "Why" vào dự án hiện tại**: Tự đặt ra và giải quyết thêm nhiều trường hợp ngoại lệ (Edge cases) cho các tính năng đang phát triển nhằm rèn luyện tính liêm chính (Integrity) trong công việc.
- **Xây dựng chuỗi kỷ luật tự học**: Sử dụng phương pháp gạch lịch/tích chuỗi (Loss Aversion) kết hợp quy tắc 2 phút để duy trì thói quen học tập các công nghệ mới (như các dịch vụ AWS Lambda, Bedrock) ít nhất 15-30 phút mỗi ngày.
- **Nghiên cứu phương pháp BMX**: Thử nghiệm cài đặt và ứng dụng phương pháp BMX từ GitHub vào các project cá nhân hoặc project nhóm để tối ưu hóa quy trình phân tách tác vụ và quản lý mã nguồn bằng AI.
- **Thúc đẩy kỹ năng collab nhóm**: Tích cực tham gia đóng góp, chia sẻ kiến thức trong các buổi Tech Meetup cộng đồng để rèn luyện kỹ năng thuyết trình, phản biện và mở rộng mạng lưới mối quan hệ (Network).

### Trải nghiệm trong event

Tham gia workshop **“FCAJ Community Day”** là một trải nghiệm cực kỳ đắt giá, giúp tôi không chỉ khai phóng tư duy về mặt kỹ thuật (Prompt nâng cao, AI Agents, Serverless) mà còn nhận được những bài học định hướng nghề nghiệp vô cùng thực tế từ các chuyên gia đi trước. Một số trải nghiệm nổi bật:

#### Học hỏi từ các diễn giả có chuyên môn cao

- Được lắng nghe những chia sẻ thẳng thắn, thực tế từ anh Khang (Cloud Kinetics) và chị Thảo (VIB) về môi trường doanh nghiệp hiện đại. Những góc nhìn về cách nhà tuyển dụng đánh giá ứng viên đã giúp tôi đập tan những ngộ nhận về điểm số hay việc lạm dụng AI khi làm bài test.

#### Trải nghiệm kỹ thuật thực tế

- Được trực tiếp quan sát bản demo kiến trúc hệ thống Serverless của dự án Prompt Optimizer ứng dụng Amazon Bedrock, DynamoDB và Lambda.
- Tiếp cận với các khái niệm nâng cao của kỹ nghệ prompt như Tree of Thought (ToT) hay Chain of Thought (CoT) thông qua các ví dụ trực quan bằng sơ đồ tư duy bước một.
- Hiểu rõ cơ chế vận hành bài bản của một hệ sinh thái AI Agent thông qua phương pháp BMX để phân tách vai trò một cách khoa học từ khâu Planning đến Review/QA.

#### Ứng dụng công cụ hiện đại

- Học cách làm chủ công cụ AI để gia tăng năng suất thay vì biến mình thành nạn nhân của hiệu ứng khuếch đại AI (AI Amplification). Biết cách tận dụng tối đa sức mạnh của AI trong việc brainstorm và phát triển ý tưởng nhưng vẫn làm chủ hoàn toàn tư duy cốt lõi.

#### Kết nối và trao đổi

- Sự kiện mang lại bầu không khí kết nối cởi mở, khuyến khích các bạn sinh viên mạnh dạn đặt câu hỏi và phản biện. Workshop tạo động lực rất lớn cho tôi trong việc chủ động chia sẻ kiến thức với cộng đồng mà không sợ sai, vì "sai lầm là một phần của hành trình tích lũy trải nghiệm".

#### Bài học rút ra

- Người chiến thắng trong hành trình phát triển bản thân không phải là người thông minh nhất mà là người kiên trì, xây dựng được cho mình một môi trường học tập tốt nhất.
- AI sẽ không thay thế lập trình viên, nhưng lập trình viên biết cách tư duy bản chất và làm chủ công cụ AI (như kỹ thuật prompt nâng cao, mô hình AI Agent) sẽ thay thế những người còn lại.
- Luôn giữ vững sự liêm chính trong công việc, đặt câu hỏi "Why" cho mọi quyết định kỹ thuật và luôn nhìn nhận lộ trình sự nghiệp theo một tầm nhìn dài hạn.

#### Một số hình ảnh khi tham gia sự kiện

- ![Ảnh minh chứng: tham gia event](</AWS-HUTECHintership-report/images/4-EventParticipated/Event1/event1(0).jpg>)
- ![Ảnh minh chứng: tham gia event](</AWS-HUTECHintership-report/images/4-EventParticipated/Event1/event1(1).jpg>)
  > Tổng thể, sự kiện không chỉ cung cấp những kiến thức kỹ thuật chuyên sâu về Prompt Engineering và mô hình phát triển phần mềm thời đại AI, mà quan trọng hơn hết là truyền ngọn lửa đam mê, kỷ luật tự học và định hình một tư duy nghề nghiệp đúng đắn cho thế hệ kỹ sư công nghệ tương lai.
