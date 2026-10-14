# Lookup: realtime events, jobs, outbox

id: lookup:events

Event or job name to the file that documents it.

| id | file | note |
|---|---|---|
| call.ended | ../events/realtime.md | Voice call kết thúc (mọi lý do — kể cả server tự end hết free window). |
| friend.message | ../events/realtime.md | Message mới trong chat 1-1 lâu dài giữa 2 bạn (khác chat ẩn danh Soul Match). |
| friend.streak.at_risk | ../events/realtime.md | Cron cảnh báo streak sắp mất (chưa nhắn hôm nay, qua mốc giờ cảnh báo) — không ghi streak. |
| friend.streak.increased | ../events/realtime.md | Streak vừa tăng và chạm mốc milestone (STREAK_MILESTONE_DAYS) — không bắn cho ngày thường. |
| gift.sent | ../events/realtime.md | Quà tặng trong Party Room — publish SAU khi transaction tiền commit (docs/10 § Gift). |
| match.confirmed | ../events/realtime.md | Cả 2 đã confirm — session bắt đầu. |
| match.matched | ../events/realtime.md | Ticket được ghép cặp — chờ 2 bên confirm. |
| minigame.session.resolved | ../events/realtime.md | Mini Game: ván oẳn tù tì đã resolve — publish SAU KHI CẢ HAI đã nộp move (docs/services/ mini-game-service.md… |
| minigame.session.started | ../events/realtime.md | Mini Game: ván oẳn tù tì mới được tạo (docs/services/mini-game-service.md § 5). |
| movie.reaction.sent | ../events/realtime.md | Movie Match ẩn danh: reaction emoji nổi trên video — hiệu ứng ephemeral, KHÔNG persist, KHÔNG kèm userId (2 b… |
| movie.session.ended | ../events/realtime.md | Movie Match: phiên xem chung kết thúc (chủ động rời — chưa có nhánh hết hạn tự động). |
| movie.session.started | ../events/realtime.md | Movie Match: phiên xem chung mới được tạo (docs/services/movie-match-service.md § 5). |
| movie.state.changed | ../events/realtime.md | Movie Match: playback state đổi (vị trí/play-pause) — last-write-wins, không lock. |
| party.comment.created | ../events/realtime.md | Comment mới trong Party Room — publish sau khi comment commit. |
| party.host.disconnected | ../events/realtime.md | Host rớt kết nối NGOÀI Ý MUỐN (webhook participant_left) — phòng CHƯA đóng, đang trong grace chờ host tự kết … |
| party.host.reconnected | ../events/realtime.md | Host đã kết nối lại trong lúc grace — huỷ lịch đóng phòng. |
| party.member.disconnected | ../events/realtime.md | Member rớt mạng ngoài ý muốn; vẫn active trong grace period để reconnect. |
| party.member.joined | ../events/realtime.md | Member vào/ra Party Room (fanout cho member active còn lại trong phòng). |
| party.member.left | ../events/realtime.md |  |
| party.member.reconnected | ../events/realtime.md | Member reconnect trước khi grace hết. |
| party.role.changed | ../events/realtime.md | Host cấp/thu quyền speaker — grant SFU đã đổi xong ở server trước khi publish. |
| party.room.closed | ../events/realtime.md | Phòng đóng (host rời, hết member, sweeper) — client phải rời UI phòng. |
| party.speaker.invite.received | ../events/realtime.md | Host gửi lời mời speaker — chỉ fanout tới audience được mời. |
| soul.ended | ../events/realtime.md | Một thành viên rời Soul Match — client phải đóng phòng ẩn danh ngay. |
| soul.matched | ../events/realtime.md | Cả 2 cùng "Thích" — đã match, profile unlock. |
| soul.message | ../events/realtime.md | Message chat ẩn danh mới trong phòng Soul Match. |
