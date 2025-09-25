# lab03
 UML Thiết kế (Use case-UC, Sequence Diagram-SQ)

 
![Use Case Diagram](https://github.com/n23dcpt013-wq/lab03/blob/main/ATM_UseCase_Withdraw.drawio.png)


Khách hàng: Người sử dụng ATM, thực hiện thao tác đưa thẻ, nhập PIN, chọn rút tiền, nhận tiền và thẻ  
ATM UI: Giao diện màn hình và bàn phím, hiển thị thông báo và nhận input từ khách hàng  
ATM Controller: Bộ điều khiển trung tâm của máy ATM, xử lý logic và điều phối các thiết bị 
Card Reader: Thiết bị nhận/thả/giữ thẻ  
Hệ thống Ngân hàng (Core Banking): Kiểm tra PIN, xác thực, kiểm tra số dư và phê duyệt giao dịch  
Cash Dispenser: Thiết bị phát tiền mặt  
Receipt Printer: Máy in biên lai (tuỳ chọn) 

 
 các thông điệp trao đổi
InsertCard(): Khách hàng đưa thẻ vào máy → Card Reader báo cho ATM Controller  
EnterPIN(pin): Khách hàng nhập PIN, ATM Controller gửi tới Core Banking để xác thực  
AuthResult(OK/FAIL): Ngân hàng trả về kết quả xác thực 
    Nếu sai → ATM thông báo nhập lại, quá số lần thì giữ thẻ  
    Nếu đúng → cho phép chọn chức năng  
Select("Withdraw")và EnterAmount(amount): Khách hàng chọn rút tiền và nhập số tiền  
DebitRequest(): ATM Controller gửi yêu cầu ghi nợ đến hệ thống ngân hàng  
Approved/Declined: Ngân hàng phản hồi. Nếu Declined (không đủ số dư, vượt hạn mức) → ATM thông báo lỗi  
Dispense(amount): ATM điều khiển Cash Dispenser phát tiền 
DispenseResult(): Kết quả phát tiền. Nếu lỗi → ATM hủy giao dịch, không trừ tiền
Print(): In biên lai (tùy chọn)  
EjectCard(): ATM trả thẻ, kết thúc giao dịch

## Sequence Diagram – ATM Rút tiền  

```mermaid
sequenceDiagram
    autonumber
    actor KhachHang as Khách hàng
    participant ATM_UI as ATM UI
    participant ATM_Core as ATM Controller
    participant Card as Card Reader
    participant Core as Hệ thống Ngân hàng
    participant Dispenser as Bộ phát tiền
    participant Printer as Máy in biên lai

    KhachHang->>Card: InsertCard()
    Card-->>ATM_Core: CardInserted
    ATM_Core->>ATM_UI: Display("Nhập PIN")
    KhachHang->>ATM_UI: EnterPIN(pin)
    ATM_UI-->>ATM_Core: SubmitPIN(pin)
    ATM_Core->>Core: AuthorizePIN(PAN, pin)
    Core-->>ATM_Core: AuthResult(OK/FAIL)

    alt PIN sai
        ATM_Core-->>ATM_UI: Display("PIN sai, nhập lại")
    else PIN đúng
        KhachHang->>ATM_UI: Select("Withdraw")
        ATM_Core->>ATM_UI: Display("Nhập số tiền")
        KhachHang->>ATM_UI: EnterAmount(amount)
        ATM_UI-->>ATM_Core: SubmitAmount(amount)
        ATM_Core->>Core: DebitRequest(PAN, amount)
        Core-->>ATM_Core: Approved(txId, newBalance) / Declined(reason)

        alt Declined
            ATM_Core-->>ATM_UI: Display("Không đủ số dư / vượt hạn mức")
            ATM_Core->>Card: Eject()
        else Approved
            ATM_Core->>Dispenser: Dispense(amount)
            Dispenser-->>ATM_Core: DispenseResult(OK/JAM/OutOfCash)

            alt Lỗi phát tiền
                ATM_Core->>Core: Reversal(txId)
                ATM_Core-->>ATM_UI: Display("Lỗi phát tiền")
            else Thành công
                opt In biên lai
                    ATM_Core->>Printer: Print(txId, amount, time, balance)
                    Printer-->>ATM_Core: PrintDone()
                end
                ATM_Core->>Card: Eject()
                ATM_Core-->>ATM_UI: Display("Vui lòng nhận tiền & thẻ")
            end
        end
    end

