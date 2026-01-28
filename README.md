<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Telegram Like – Full HTML</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial}
body{background:#e5e5e5;height:100vh}
.hidden{display:none}
.btn{background:#0088cc;color:#fff;border:none;padding:10px 18px;border-radius:20px;cursor:pointer}
.btn:hover{background:#0077b3}
input{width:100%;padding:10px;border-radius:20px;border:1px solid #ccc;outline:none}

/* LOGIN */
#login{height:100vh;display:flex;justify-content:center;align-items:center}
.login-box{background:#fff;width:320px;padding:25px;border-radius:12px}
.login-box h2{text-align:center;margin-bottom:15px}
.login-box input{margin-bottom:10px}

/* APP */
#app{display:flex;height:100vh}

/* SIDEBAR */
.sidebar{width:30%;background:#fff;border-right:1px solid #ddd}
.sidebar-header{background:#0088cc;color:#fff;padding:15px;font-weight:bold}
.chat-item{padding:12px;border-bottom:1px solid #eee;cursor:pointer}
.chat-item:hover{background:#f5f5f5}
.chat-item small{color:#777}

/* CHAT */
.chat{width:70%;display:flex;flex-direction:column}
.chat-header{background:#fff;padding:15px;border-bottom:1px solid #ddd}
.messages{flex:1;padding:15px;overflow-y:auto}
.msg{max-width:65%;padding:10px;margin-bottom:8px;border-radius:10px}
.left{background:#fff}
.right{background:#0088cc;color:#fff;margin-left:auto}

/* INPUT */
.input{display:flex;padding:10px;background:#fff;border-top:1px solid #ddd}
.input input{flex:1}
.voice{width:40px;height:40px;border-radius:50%;border:none;background:#eee;margin-right:8px}

/* ADMIN */
#admin{padding:20px}
.admin-box{background:#fff;padding:20px;border-radius:10px}
table{width:100%;border-collapse:collapse}
td,th{border-bottom:1px solid #ddd;padding:8px}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login">
  <div class="login-box">
    <h2>Login</h2>
    <input id="phone" placeholder="Phone number">
    <input id="otp" placeholder="OTP (1234)">
    <button class="btn" onclick="login()">Login</button>
  </div>
</div>

<!-- APP -->
<div id="app" class="hidden">
  <div class="sidebar">
    <div class="sidebar-header">
      Telegram Like
      <button style="float:right" onclick="openAdmin()">Admin</button>
    </div>
    <div class="chat-item" onclick="openChat('private')">
      <b>Private Chat</b><br><small>Online</small>
    </div>
    <div class="chat-item" onclick="openChat('group')">
      <b>Group</b><br><small>5 members</small>
    </div>
    <div class="chat-item" onclick="openChat('channel')">
      <b>Channel</b><br><small>1200 subs</small>
    </div>
  </div>

  <div class="chat">
    <div class="chat-header" id="chatTitle">Private Chat</div>
    <div class="messages" id="messages"></div>

    <div class="input">
      <button class="voice" onclick="voice()">🎤</button>
      <input id="text" placeholder="Type message">
      <button class="btn" onclick="send()">Send</button>
    </div>
  </div>
</div>

<!-- ADMIN -->
<div id="admin" class="hidden">
  <div class="admin-box">
    <h3>Admin Panel</h3>
    <table>
      <tr><th>User</th><th>Status</th><th>Action</th></tr>
      <tr><td>Nahid</td><td>Active</td><td><button class="btn">Ban</button></td></tr>
      <tr><td>User2</td><td>Muted</td><td><button class="btn">Unmute</button></td></tr>
    </table>
    <br>
    <button class="btn" onclick="closeAdmin()">Back</button>
  </div>
</div>

<script>
/* STATE */
let currentChat="private";
const channel=new BroadcastChannel("telegram_like");

/* LOGIN */
function login(){
  if(document.getElementById("otp").value==="1234"){
    document.getElementById("login").classList.add("hidden");
    document.getElementById("app").classList.remove("hidden");
    load();
  }else alert("Wrong OTP");
}

/* CHAT */
function openChat(type){
  currentChat=type;
  document.getElementById("chatTitle").innerText=type.toUpperCase();
  load();
}

function send(){
  const t=document.getElementById("text");
  if(!t.value)return;
  const msg={chat:currentChat,text:t.value,me:true,time:Date.now()};
  save(msg);
  channel.postMessage(msg);
  t.value="";
}

channel.onmessage=e=>{
  if(e.data.chat===currentChat && !e.data.me){
    render(e.data,false);
  }
};

function save(msg){
  let data=JSON.parse(localStorage.getItem("chat")||"[]");
  data.push(msg);
  localStorage.setItem("chat",JSON.stringify(data));
  render(msg,true);
}

function load(){
  document.getElementById("messages").innerHTML="";
  let data=JSON.parse(localStorage.getItem("chat")||"[]");
  data.filter(m=>m.chat===currentChat).forEach(m=>{
    render(m,m.me);
  });
}

function render(m,me){
  const d=document.createElement("div");
  d.className="msg "+(me?"right":"left");
  d.innerText=m.text;
  document.getElementById("messages").appendChild(d);
}

/* VOICE */
function voice(){
  alert("🎤 Voice UI ready (HTML only)");
}

/* ADMIN */
function openAdmin(){
  document.getElementById("app").classList.add("hidden");
  document.getElementById("admin").classList.remove("hidden");
}
function closeAdmin(){
  document.getElementById("admin").classList.add("hidden");
  document.getElementById("app").classList.remove("hidden");
}
</script>

</body>
</html>
