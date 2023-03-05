# Charles Proxy Instagram Requests
Instagram Private API Requests Exported From Charles Proxy

Requests are separated by Instagram's Android versions


# How to Contibute:

### Note: You should have a Facebook account and an Instagram account associated with it. (Login to Instagram with Facebook)

1. [Go to Researcher Settings on Facebook](https://www.facebook.com/whitehat/researcher-settings/)

2. Check "Enable user installed Certificate Authorities (CAs) for your Facebook account" and "Enable user installed CAs for your Whitehat Test Accounts."

3. Choose Instagram in "Select on which apps you want to enable the Mobile Settings."

4. Force Stop Instagram and Clear Data.

5. Login to Instagram with Your Facebook Account.

6. Go to Instagram's Settings -> Internal -> Whitehat Settings

7. Check "Allow user installed certificates" and "Do not use TLS 1.3"

8. Force Stop Instagram and Clear Data

9. Set WIFI's proxy to Charles Proxy Address

10. Open Instagram and Export Requests in Charles Session File Format (.chls) (note: Give it a Suitable Name)

11. Move the exported file to its related Instagram android version.

#### Pull Request!

Cheers 🥳

# How to Find Version, VersionCode and Signature Key?

Check User-Agent Header in Requests for version and version code:

![Check User-Agent Header in Requests for version and version code](https://i.ibb.co/grTDTGD/version-code.png)

## To Find out Signature
1. Install Frida using `pip install frida`
2. Run [frida-server](https://github.com/frida/frida/releases) on your device using adb.
3. Run Instagram on Your Phone or Emulator
4. Put Below Code in a File Name `script.js` and Run Using Python (`python script.py`):
```python
import frida, sys

def on_message(message, data):
    print(message)

process = frida.get_usb_device().attach('com.instagram.android')

jscode = """
Interceptor.attach(Module.findExportByName("libscrambler.so", "_ZN9Scrambler9getStringESs"), {
    onLeave: function (retval) {
        console.log(Memory.readCString(retval));
    }
});
"""

script = process.create_script(jscode)
script.on('message', on_message)
print('[*] Running sniffer')
script.load()
sys.stdin.read()
```
5. Make a Request in Instagram to Get Signature Printed:

![Signature](https://i.ibb.co/2kqDpcy/signature.png)
-------------------------------------------



CÔNG CỤ CẦN THIẾT:
Điện thoại android đã root hoặc máy ảo cũng được (trong bài viết mình dùng Samsung Note 8)
Máy tính đã cài:
python vì các tool trong bài viết được xây dựng bằng python
adb đương nhiên vì đang làm trên máy android mà 🤪
Tải về file frida server cho điện thoại: https://github.com/frida/frida/releases (tải đúng phiên bản server như máy của mình sẽ tải bản server cho android-arm64)
Charles hoặc Burp Suite tùy bạn quen dùng cái nào hơn (trong bài mình dùng Charles)
SSL Pinning Bypass:
    Đầu tiên chúng ta cần cài cắm nằm vùng vào điện thoại, cái mà sẽ theo lệnh của chúng ta và làm theo yêu cầu => đó chính là Frida Server. Các bạn giải nén file frida server đã tải về ra và đổi tên nó thành frida-server để tiện dùng cho mấy lệnh phía dưới đỡ phải đổi lại.
Dùng lệnh adb sau để đẩy vào điện thoại:

adb push frida-server /data/local/tmp

Dùng tiếp lệnh sau để truy cập vào điện thoại:

adb shell

Sau khi truy cập thì dùng các lệnh dưới để chạy frida server (mình ko để frida server chạy background mà để nó chạy foreground vì không thích 👎 )


cd /data/local/tmp
chmod +x frida-server
./frida-server

Bạn có thể thích những bài đăng này
FRIDA (SSL Pinning ByPass) Tích hợp tự động nhận tiền MOMO cho website
FRIDA (SSL Pinning ByPass) Tích hợp tự động nhận tiền MOMO cho website
Hướng dẫn cài V2Ray lên OpenWrt  để dùng 4G miễn phí port  80 (đơn  giản)
Hướng dẫn cài V2Ray lên OpenWrt để dùng 4G miễn phí port 80 (đơn giản)
Để nguyên terminal đang chạy frida server đó qua một bên, khi nào xong việc chỉ cần tắt đi là xong (tắt luôn frida server đang chạy foreground)
Tiếp tục cài objection bằng PIP:

pip3 install objection

Sau khi cài xong thì chạy lệnh dưới để bắt đầu ssl pinning bypass với momo:


objection -g com.mservice.momotransfer explore
android sslpinning disable




LẤY REQUEST:
    Ở đây mình dùng app Charles. Sau khi cài xong file Cert và config proxy (cái này dễ quá mà viết ra chi tiết thì dài) thì các bạn bắt cho mình 2 URL quan trọng sau:
https://owa.momo.vn/public/login
https://m.mservice.io/hydra/v2/user/noti
URL số 1 là khi các bạn nhập mã 6 số (hoặc quét vân tay) sẽ gọi và trả về một JSON. Trong JSON trả về đó các bạn chỉ cần lưu ý đến AUTH_TOKEN.
AUTH_TOKEN này có giá trị là 1giờ 30 phút được dùng ở Header Authorization số 2

URL số 2 để lấy các thông báo theo khoảng thời gian (quy định trong body). Kết quả trả về là một JSON trong đó chỉ cần quan tâm đến message->data->notifications là một Array. Trong Array này sẽ chứa mọi thông báo, trong đó có cả các thông báo về giao dịch 😍





TÍCH HỢP VÀO WEBSITE:
    Cái này thì tùy code của các bạn sử dụng ngôn ngữ nào mà vận dụng 2 request đó cho phù hợp. Tuy nhiên, thì mình sẽ có một số gợi ý thế này:
Giữ nguyên Request số 1 lặp lại mỗi 1 giờ 20 phút 1 lần để lấy AUTH_TOKEN và lưu lại vào cache
Lặp lại request số 2 mỗi 10 phút 1 lần với:
AUTH_TOKEN lấy ở cache
fromTime là timestamp micro second của NOW - (15*60*1000)
toTime là timestamp micro second của NOW
Mỗi giao dịch sẽ có tranId khác nhau, chỉ cần so sánh tranId để xem có giao dịch mới hay ko
Vậy là xong rồi, anh em có thể scan thử hình bên dưới để tặng mình một ly cafe. Sau đó là một lời cảm ơn tự động vô cùng nồng ấm từ mình 🥳

