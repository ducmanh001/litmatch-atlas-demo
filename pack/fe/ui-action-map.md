# UI action map (screen -> actions -> destination)

id: fe:ui-action-map

Source: layouts/litmatch-ui-action-map.svg (human-authored UI action map (layouts/); not verified against code). Use it for intended UX; the route files show what the code really does.

## HOME /home

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Dashboard — trung tâm điều hướng

Header actions
- Avatar / nickname → Profile
- Diamond → Wallet
- Bell → Notifications
- Theme / Language → local UI

Main actions
- Tìm quanh đây → Discovery
- Voice Match → Matching
- Party / Feed / Friends / Video
- Soul / Movie / Palm Match

## DISCOVERY /discovery

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Browse người phù hợp

Browse actions
- Lọc mọi người
- Lọc giới tính
- Lọc độ tuổi
- Quanh đây / vị trí

Profile actions
- Mở hồ sơ → Public Profile
- Mời Soul Match
- Mời Voice Match
- Quay lại Home / Matching

## MATCHING /matching

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Soul Match / Voice Match

Queue actions
- Chọn Soul hoặc Voice
- Chọn giới tính mong muốn
- Bắt đầu tìm kiếm
- Huỷ tìm kiếm

Session actions
- Soul → Chat ẩn danh
- Voice → LiveKit / microphone
- Thích / Bỏ qua
- Match → Friends / Chat

## PARTY /party

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Phòng trò chuyện nhiều người

List actions
- Tìm kiếm phòng
- Lọc chủ đề
- Xem thêm phòng
- Tạo phòng mới

Room actions
- Join → Party Room
- Chat realtime
- Bật / tắt mic
- Tặng quà / Rời phòng

## FEED /feed

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Bài viết cộng đồng

Content actions
- Xem danh sách bài viết
- Tạo bài viết
- Chọn audience
- Đính kèm media / emoji

Post actions
- Like / Unlike
- Comment
- Share
- Mở profile tác giả

## FRIENDS /friends

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Danh sách bạn bè và inbox

List actions
- Tìm kiếm bạn bè
- Mở friend profile
- Mở conversation
- Xem unread count

Conversation actions
- Gửi message / ảnh
- Mark read / mute
- Gọi voice → Call
- Xem chung Movie

## PROFILE /profile

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Hồ sơ của tôi

Profile actions
- Xem nickname / avatar
- Xem Diamond / bạn bè / posts
- Chỉnh sửa → Profile Edit
- Mở Wallet / Privacy / Help

Account actions
- Đổi ngôn ngữ
- Đổi theme
- Đăng xuất → Login
- Mở public profile người khác

## VIDEO /video

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Short video feed

Playback actions
- For You / Following
- Play / pause / mute
- Next / previous reel
- Mở profile tác giả

Engagement actions
- Like / Unlike
- Comment
- Tặng quà
- Voice Match → Matching

## WALLET /wallet

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Diamond và VIP

Wallet actions
- Xem balance
- Xem VIP tier / expiry
- Xem package catalog
- Nạp Diamond

Commerce actions
- Chọn gói VIP
- Tạo payment order
- Kiểm tra payment
- Diamond → Gift / Matching

## MORE /help /privacy

section: MAIN SCREENS — các entry point chính sau khi đăng nhập
Settings, support, account

Navigation actions
- Xem/chỉnh sửa Profile
- Mở Discovery / Video / Party / Feed
- Premium / Wallet
- Mời bạn bè

Settings actions
- Theme / Language
- Privacy / Block / Report
- FAQ / Gửi feedback
- Logout → Login

## PUBLIC PROFILE /users/:id

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Từ Discovery / Feed / Video / Profile


- Profile data / presence / posts
- Follow / Unfollow
- Nhắn tin / mở conversation
- Tặng quà để mở chat
- Quay lại Friends / Chat

## PROFILE EDIT /profile/edit

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Từ Profile / More


- Đổi avatar
- Nickname / ngày sinh
- Giới tính / sở thích
- Đang tìm kiếm / độ tuổi
- Lưu → cập nhật Home/Profile

## MATCH / PARTY SESSION

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Từ Matching / Party / Movie / Palm


- Soul: anonymous chat / verdict
- Voice: call / mic / like / end
- Party: member / chat / stage
- Movie: playback sync / chat
- Palm: flip / result

## CHAT /chat/:friendUserId

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Từ Friendship / Profile / Friends


- Gửi text / ảnh
- Mark read / mute
- Gọi voice → /call
- Movie Match với bạn
- Notification khi có message

## NOTIFICATION / SUPPORT

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Từ Header / Profile / More


- Notification unread / list
- Mark notification read
- Điều hướng tới Chat / Profile
- FAQ / tạo support ticket
- Privacy / block / report

## END-TO-END UI CHAIN

section: SHARED DESTINATIONS — các màn được nhiều màn khác gọi tới
Home → Discovery → Public Profile → Follow / Nhắn tin → Friends → Chat → Call / Movie



- Home → Matching → Soul / Voice Session → Like hai chiều → Friendship → Friends / Chat
- Home → Party → Party Room → Join → Realtime Chat / Mic / Gift
- Home → Wallet → Diamond / VIP → Gift / Matching / Premium actions

## Admin / Staff

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app




## Admin Login

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app




## RequireAuth + Role

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app




## Admin Shell + Sidebar

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
theme · language · command search · notifications · logout



## DASHBOARD /

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Admin app — tổng quan vận hành

View actions
- New users / active users
- Diamond spent / user tiers
- Live rooms / top rooms
- Daily chart / recent activities

Navigate
- Mở Users / Moderation / Rooms
- Theo dõi audit activity

## USERS /users

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Quản lý tài khoản người dùng

List actions
- Tìm theo nickname / status
- Lọc / phân trang
- Mở hồ sơ chi tiết
- Xem role / guest / status

Account actions
- Ban / Unban → user app
- Ghi audit action

## MODERATION /moderation

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Reports · video · support

Tabs / filters
- Reports: pending / resolved / dismissed
- Pending videos / Published videos
- Support: open / progress / resolved / closed

Review actions
- Resolve / dismiss / bulk case
- Approve / reject / remove video
- Reply / assign status ticket

## ECONOMY /economy

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Wallet và giao dịch người dùng

Lookup actions
- Nhập user ID / tra wallet
- Xem balance / earnings
- Xem transaction history

Ledger actions
- Xem loại / trạng thái / delta
- Refund transaction → audit
- Nhập lý do hoàn tiền

## GIFTS /gifts

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Catalog quà tặng

Catalog actions
- Tạo gift: code / name / price
- Xem danh sách và trạng thái
- Sửa price Diamond
- Enable / Disable gift

Effect
- Catalog cập nhật cho Feed / Party / Chat
- Ghi audit activity

## ROOMS /rooms

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Giám sát Party Room live

Room actions
- Xem live room / host / title
- Xem member / trạng thái
- Đóng room → Party app
- Dashboard xem top rooms

Operational effect
- Room biến mất khỏi danh sách live
- Ghi admin activity

## CONFIG /config

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Catalog commerce và broadcast

Config actions
- Bật / ẩn IAP product
- Bật / ẩn VIP plan
- Xem price / diamonds / days
- Kiểm tra catalog user-facing

Notification actions
- Soạn title / body / audience
- Broadcast → user Notifications

## PERMISSIONS /permissions

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Role và policy của staff

Policy actions
- Xem permission matrix
- Bật / tắt quyền Moderator
- Bật / tắt quyền Admin
- Bảo vệ manage_permissions

Staff actions
- Đổi role staff
- Thu hồi quyền → user role

## ADMIN EFFECTS / AUDIT / APP DATA

section: ADMIN DASHBOARD — staff console riêng, có role guard và action tác động tới dữ liệu app
Điểm nối từ thao tác Admin về các màn user và backend


- Users: ban / unban → trạng thái tài khoản, access App Shell
- Moderation: report / video / support → Feed, Video, Support, notification
- Economy / Gifts / Config → Wallet, Gift, VIP, payment, catalog, broadcast
- Rooms → Party list / live room hiện tại
- Permissions → route guard và action guard của Admin
- Mọi mutation nhạy cảm → audit log / recent activities

Admin chain: Login → Role Guard → Screen → Mutation → App user data / Audit


