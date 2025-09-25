# lab03
 UML Thiết kế (Use case-UC, Sequence Diagram-SQ)

 
![Use Case Diagram](https://github.com/n23dcpt013-wq/lab03/blob/main/ATM_UseCase_Withdraw.drawio.png)


Khách hàng: Người sử dụng ATM, thực hiện thao tác đưa thẻ, nhập PIN, chọn rút tiền, nhận tiền và thẻ.  
ATM UI: Giao diện màn hình và bàn phím, hiển thị thông báo và nhận input từ khách hàng.  
ATM Controller: Bộ điều khiển trung tâm của máy ATM, xử lý logic và điều phối các thiết bị.  
Card Reader: Thiết bị nhận/thả/giữ thẻ.  
Hệ thống Ngân hàng (Core Banking): Kiểm tra PIN, xác thực, kiểm tra số dư và phê duyệt giao dịch.  
Cash Dispenser: Thiết bị phát tiền mặt.  
Receipt Printer: Máy in biên lai (tuỳ chọn).  

 
 các thông điệp trao đổi
InsertCard(): Khách hàng đưa thẻ vào máy → Card Reader báo cho ATM Controller.  
EnterPIN(pin): Khách hàng nhập PIN, ATM Controller gửi tới Core Banking để xác thực.  
AuthResult(OK/FAIL): Ngân hàng trả về kết quả xác thực.  
    Nếu sai → ATM thông báo nhập lại, quá số lần thì giữ thẻ.  
    Nếu đúng → cho phép chọn chức năng.  
Select("Withdraw")và EnterAmount(amount): Khách hàng chọn rút tiền và nhập số tiền.  
DebitRequest(): ATM Controller gửi yêu cầu ghi nợ đến hệ thống ngân hàng.  
Approved/Declined: Ngân hàng phản hồi. Nếu Declined (không đủ số dư, vượt hạn mức) → ATM thông báo lỗi.  
Dispense(amount): ATM điều khiển Cash Dispenser phát tiền.  
DispenseResult(): Kết quả phát tiền. Nếu lỗi → ATM hủy giao dịch, không trừ tiền.  
Print(): In biên lai (tùy chọn).  
EjectCard(): ATM trả thẻ, kết thúc giao dịch.  
