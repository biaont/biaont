addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})
  
async function handleRequest(request) {
  const phone = '133239107423' // 车主的手机号
  const wxpusherAppToken = 'AT_*******************' // Wxpusher APP Token
  const wxpusherUIDs = ['UID_**********************'] // 车主的UIDs
  
  const htmlContent = `
    <!DOCTYPE html>
    <html lang="zh-CN">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>微信消息通知</title>
        <style>
          * { box-sizing: border-box; margin: 0; padding: 0; }
          body { font-family: Arial, sans-serif; display: flex; align-items: center; justify-content: center; height: 100vh; background: #f0f2f5; color: #333; }
          .container { text-align: center; padding: 20px; width: 100%; max-width: 400px; border-radius: 8px; box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); background: #fff; }
          h1 { font-size: 24px; margin-bottom: 20px; color: #007bff; }
          p { margin-bottom: 20px; font-size: 16px; color: #555; }
          button { 
            width: 100%; 
            padding: 15px; 
            margin: 10px 0; 
            font-size: 18px; 
            font-weight: bold; 
            color: #fff; 
            border: none; 
            border-radius: 6px; 
            cursor: pointer; 
            transition: background 0.3s; 
          }
          .notify-btn { background: #28a745; }
          .notify-btn:hover { background: #218838; }
          .call-btn { background: #17a2b8; }
          .call-btn:hover { background: #138496; }
        </style>
      </head>
      <body>
        <div class="container">
          <h1>微信消息通知</h1>
          <p>如需通知，请点击以下按钮</p>
          <button class="notify-btn">微信消息通知</button>
          <button class="call-btn">拨打电话通知</button>
        </div>
  
        <script>
          // 调用 Wxpusher API 来发送挪车通知
          function notifyOwner() {
            // 弹出提示框获取用户输入
            const userMessage = prompt("请输入需要通知的内容：", "您好，有人需要您挪车，请及时处理。");
             
            // 如果用户点击取消或没有输入内容，则返回
            if (!userMessage) {
                return;
            }
         
            fetch("https://wxpusher.zjiecode.com/api/send/message", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({
                    appToken: "${wxpusherAppToken}",
                    content: userMessage,  // 使用用户输入的内容
                    contentType: 1,
                    uids: ${JSON.stringify(wxpusherUIDs)}                 })
            })
            .then(response => response.json())
            .then(data => {
                if (data.code === 1000) {
                    alert("通知已发送！");
                } else {
                    alert("通知发送失败，请稍后重试。");
                }
            })
            .catch(error => {
                console.error("Error sending notification:", error);
                alert("通知发送出错，请检查网络连接。");
            });
        }
  
          // 拨打车主电话
          function callOwner() {
            window.location.href = "tel:${phone}";
          }
        </script>
      </body>
    </html>
  `
  
  return new Response(htmlContent, {
    headers: { 'Content-Type': 'text/html;charset=UTF-8' },
  })
}
