# ERC721-reentrancy
# Reentrancy là tình huống lỗi khi thiết kế trong 1 hàm vô tình làm hàm đó gọi lại chính hàm đó hoặc 1 hàm khác trước khi cập nhật biến trạng thái mới
Điều này có thể gây ra tấn công reentrancy khiến contract bị rút hết tiền

# Ví dụ đơn giản nhất về reentrancy tại contracts1
tại đây .call được gọi trước khi biến số dư được cập nhật
khi .call được gọi thì hàm fallback của contract được .call gọi sẽ được thực thi
tại đây hàm fallback gọi tiếp hàm withdraw để rút hết tiền vì khi này số dư của kẻ tấn công chưa được cập nhật

# Ví dụ phức tạp hơn tại contracts2 là về ERC721
tại function mint() của nạn nhân sẽ gọi function _safeMint() trước khi cập nhật biến trạng thái alreadyMinted[msg.sender] = true;
trong function _safeMint() sẽ gọi tiếp đến hàm function onERC721Received của msg.sender để thực hiện logic tại contract của msg,sender
Hacker có thể lợi dụng điều này để gọi liên tiếp hàm mint() của nạn nhân để mint bất kì số lượng NFT nào.

#Để ngăn ngừa lỗi này
Cần phải cập nhật biến trạng thái trước khi thực thi logic hàm (tiên quyết)
Thêm modifier nonReentrancy ở trong với những function có thể gây ra lỗi reentrancy.
