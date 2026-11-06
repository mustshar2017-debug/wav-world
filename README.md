
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#08090d">
<title>WAVO – وافي العالمي</title>

<style>
:root{
--bg:#08090d;--panel:#151820;--tur:#55e6c1;
--white:#fff;--muted:#a4a9b5;
}
*{box-sizing:border-box}
body{
margin:0;background:var(--bg);color:white;
font-family:Arial,Tahoma,sans-serif;
overflow:hidden;
}
button,input,textarea{font:inherit}
button{cursor:pointer}
header{
height:55px;position:fixed;top:0;left:0;right:0;
z-index:10;display:flex;align-items:center;
justify-content:space-between;padding:0 15px;
background:#08090ddd;backdrop-filter:blur(12px);
}
.logo{font-size:25px;font-weight:900;color:var(--tur);letter-spacing:2px}
.header-actions{display:flex;gap:15px}
.header-actions button{
background:none;border:0;color:white;font-size:19px
}
#feed{
position:absolute;top:55px;bottom:65px;
left:0;right:0;overflow-y:auto;
scroll-snap-type:y mandatory;
}
.video-card{
height:100%;min-height:100%;position:relative;
scroll-snap-align:start;background:#111;
display:flex;align-items:center;justify-content:center;
overflow:hidden;
}
.video-card video{
width:100%;height:100%;object-fit:cover;
}
.video-info{
position:absolute;bottom:20px;right:15px;
left:75px;z-index:2;text-shadow:0 1px 5px #000;
}
.video-info strong{font-size:16px}
.video-info p{font-size:14px}
.actions{
position:absolute;left:10px;bottom:90px;
display:flex;flex-direction:column;gap:18px;
z-index:3;align-items:center;
}
.actions button{
background:#1119;border:0;color:white;
font-size:23px;width:45px;height:45px;
border-radius:50%;
}
.actions small{font-size:11px;display:block}
.bottom{
position:fixed;bottom:0;left:0;right:0;
height:65px;background:#101219;
display:flex;justify-content:space-around;
align-items:center;z-index:20;
border-top:1px solid #252a32;
}
.bottom button{
background:none;border:0;color:#c5c8ce;
font-size:12px;display:flex;
flex-direction:column;align-items:center;gap:4px;
}
.bottom button b{font-size:21px}
.bottom button.active{color:var(--tur)}
.bottom .plus{
width:48px;height:39px;border-radius:12px;
background:var(--tur);color:#07110e;
font-size:30px;font-weight:bold;
}
.screen{
display:none;position:fixed;inset:55px 0 65px;
overflow-y:auto;background:var(--bg);
padding:18px;z-index:5;
}
.screen.active{display:block}
.panel{
background:var(--panel);padding:17px;
border-radius:15px;margin-bottom:15px;
}
input,textarea{
width:100%;padding:13px;margin:7px 0;
border:1px solid #353b46;border-radius:10px;
background:#090c12;color:white;
}
textarea{min-height:85px;resize:vertical}
.primary{
width:100%;padding:13px;background:var(--tur);
color:#07110e;border:0;border-radius:10px;
font-weight:bold;margin-top:8px;
}
.secondary{
padding:10px;background:#252c35;color:white;
border:0;border-radius:9px;margin:4px;
}
video.preview{width:100%;max-height:300px}
.modal{
display:none;position:fixed;inset:0;background:#000d;
z-index:50;align-items:center;justify-content:center;
padding:15px;
}
.modal.open{display:flex}
.modal-box{
background:#171c25;border-radius:17px;
padding:20px;width:100%;max-width:430px;
max-height:85vh;overflow:auto;
}
.modal-box h3{margin-top:0;color:var(--tur)}
.muted{color:var(--muted);font-size:13px}
.item{padding:12px;border-bottom:1px solid #303540}
#notice{
position:fixed;top:65px;left:50%;transform:translateX(-50%);
background:#182b27;color:var(--tur);padding:12px 18px;
border-radius:12px;z-index:100;display:none;
}
</style>
</head>

<body>

<header>
<div class="logo">WAVO</div>
<div class="header-actions">
<button onclick="changeLanguage()">🌐</button>
<button onclick="loadFeed()">↻</button>
</div>
</header>

<div id="notice"></div>

<main id="feed"></main>

<section id="friends" class="screen">
<h2>الأصدقاء</h2>
<div class="panel">
<input id="friendSearch" placeholder="ابحث عن اسم المستخدم">
<button class="primary" onclick="searchFriends()">بحث</button>
<div id="friendResults"></div>
</div>
</section>

<section id="inbox" class="screen">
<h2>صندوق الوارد</h2>
<div id="messagesList" class="panel">
سجل الدخول لعرض رسائلك.
</div>
</section>

<section id="account" class="screen">
<h2>حسابي</h2>
<div class="panel">
<div id="accountInfo">لم تسجل الدخول بعد.</div>
<div id="accountActions"></div>
</div>
<div class="panel">
<h3>إحصائياتي</h3>
<div id="stats">0 فيديو</div>
</div>
<div class="panel">
<button class="secondary" onclick="showSaved()">المفضلة ❤️</button>
<button class="secondary" onclick="showMyVideos()">فيديوهاتي 🎬</button>
</div>
<div id="accountContent"></div>
</section>

<nav class="bottom">
<button onclick="showScreen('account',this)"><b>👤</b>حسابي</button>
<button onclick="showScreen('inbox',this)"><b>✉️</b>صندوق الوارد</button>
<button onclick="openUpload()" class="plus">+</button>
<button onclick="showScreen('friends',this)"><b>👥</b>الأصدقاء</button>
<button class="active" onclick="showHome(this)"><b>⌂</b>الرئيسية</button>
</nav>

<!-- Login -->
<div class="modal" id="authModal">
<div class="modal-box">
<h3>مرحباً بك في WAVO</h3>
<input id="authEmail" type="email" placeholder="البريد الإلكتروني">
<input id="authPassword" type="password" placeholder="كلمة المرور">
<input id="authName" placeholder="اسم العرض (للحساب الجديد)">
<button class="primary" onclick="signUp()">إنشاء حساب</button>
<button class="primary" onclick="signIn()">تسجيل الدخول</button>
<button class="secondary" onclick="closeModal('authModal')">إغلاق</button>
</div>
</div>

<!-- Upload -->
<div class="modal" id="uploadModal">
<div class="modal-box">
<h3>إضافة فيديو إلى WAVO</h3>
<input type="file" id="videoFile" accept="video/*">
<textarea id="videoCaption" maxlength="500" placeholder="اكتب وصف الفيديو..."></textarea>
<button class="primary" onclick="uploadVideo()">رفع الفيديو</button>
<p class="muted">اختر فيديو من جهازك. الحد الأقصى يعتمد على إعدادات التخزين.</p>
<button class="secondary" onclick="closeModal('uploadModal')">إلغاء</button>
</div>
</div>

<!-- Comments -->
<div class="modal" id="commentsModal">
<div class="modal-box">
<h3>التعليقات</h3>
<div id="commentsList"></div>
<textarea id="commentText" placeholder="اكتب تعليقك..."></textarea>
<button class="primary" onclick="sendComment()">إرسال التعليق</button>
<button class="secondary" onclick="closeModal('commentsModal')">إغلاق</button>
</div>
</div>

<!-- Chat -->
<div class="modal" id="chatModal">
<div class="modal-box">
<h3 id="chatTitle">المحادثة</h3>
<div id="chatContent"></div>
<textarea id="chatText" placeholder="اكتب رسالتك..."></textarea>
<button class="primary" onclick="sendMessage()">إرسال</button>
<button class="secondary" onclick="closeModal('chatModal')">إغلاق</button>
</div>
</div>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script>
/* WAVO - Supabase connection */
const SUPABASE_URL =
"https://jajaujilwhkroboyhenj.supabase.co";

const SUPABASE_KEY =
"sb_publishable_9-FF377xk93I2f3XhWN-3w_UkxdOZLR";

const db = window.supabase.createClient(
SUPABASE_URL,SUPABASE_KEY
);

let currentUser=null;
let currentVideo=null;
let chatUser=null;
let videos=[];
let language="ar";

const $=id=>document.getElementById(id);

function notice(message){
$("notice").textContent=message;
$("notice").style.display="block";
setTimeout(()=>$("notice").style.display="none",3000);
}

function closeModal(id){
$(id).classList.remove("open");
}

function showModal(id){
$(id).classList.add("open");
}

async function initialize(){
const {data,error}=await db.auth.getSession();
if(error) console.error(error);
currentUser=data.session?.user||null;
await updateAccount();
await loadFeed();

db.auth.onAuthStateChange(async(event,session)=>{
currentUser=session?.user||null;
await updateAccount();
await loadFeed();
});
}

async function signUp(){
const email=$("authEmail").value.trim();
const password=$("authPassword").value;
const name=$("authName").value.trim();

if(!email||password.length<6){
return notice("أدخل البريد وكلمة مرور من 6 أحرف على الأقل");
}

const {data,error}=await db.auth.signUp({
email,password
});

if(error)return notice(error.message);

if(data.user){
if(data.session){
const {error:profileError}=await db.from("profiles").upsert({
id:data.user.id,
display_name:name||email.split("@")[0],
username:email.split("@")[0]
});
if(profileError)notice("تم إنشاء الحساب لكن تعذر إنشاء الملف الشخصي: "+profileError.message);
else notice("تم إنشاء الحساب بنجاح");
}else{
notice("تحقق من بريدك الإلكتروني لتأكيد الحساب، ثم سجل الدخول");
}
}

closeModal("authModal");
await updateAccount();
}

async function signIn(){
const email=$("authEmail").value.trim();
const password=$("authPassword").value;

const {data,error}=await db.auth.signInWithPassword({
email,password
});

if(error)return notice(error.message);

currentUser=data.user;
closeModal("authModal");
notice("تم تسجيل الدخول بنجاح");
await updateAccount();
await loadFeed();
}

async function logout(){
await db.auth.signOut();
currentUser=null;
notice("تم تسجيل الخروج");
await updateAccount();
}

async function updateAccount(){
if(!currentUser){
$("accountInfo").textContent="لم تسجل الدخول";
$("accountActions").innerHTML=
'<button class="primary" onclick="showModal(\\'authModal\\')">تسجيل الدخول / إنشاء حساب</button>';
$("stats").textContent="0 فيديو";
return;
}

const {data:profile}=await db.from("profiles")
.select("*").eq("id",currentUser.id).maybeSingle();

const name=profile?.display_name||currentUser.email;
$("accountInfo").textContent=
name+" · "+currentUser.email;

$("accountActions").innerHTML=
'<button class="secondary" onclick="logout()">تسجيل الخروج</button>';

const {count}=await db.from("videos")
.select("*",{count:"exact",head:true})
.eq("user_id",currentUser.id);

$("stats").textContent=(count||0)+" فيديو";
}

function showScreen(id,button){
$("feed").style.display="none";
document.querySelectorAll(".screen").forEach(s=>s.classList.remove("active"));
$(id).classList.add("active");
document.querySelectorAll(".bottom button").forEach(b=>b.classList.remove("active"));
if(button)button.classList.add("active");
if(id==="inbox")loadMessages();
}

function showHome(button){
document.querySelectorAll(".screen").forEach(s=>s.classList.remove("active"));
$("feed").style.display="block";
document.querySelectorAll(".bottom button").forEach(b=>b.classList.remove("active"));
if(button)button.classList.add("active");
loadFeed();
}

function openUpload(){
if(!currentUser)return showModal("authModal");
showModal("uploadModal");
}

async function uploadVideo(){
if(!currentUser)return notice("سجل الدخول أولاً");

const file=$("videoFile").files[0];
if(!file)return notice("اختر فيديو أولاً");
if(!file.type.startsWith("video/"))return notice("الملف ليس فيديو");
if(file.size>100*1024*1024)return notice("الحد الأقصى للرفع 100 ميجابايت");

notice("جارٍ رفع الفيديو...");

const safeName=crypto.randomUUID()+"."+(
file.name.split(".").pop().replace(/[^a-zA-Z0-9]/g,"")||"mp4"
);
const path=currentUser.id+"/"+safeName;

const {error:uploadError}=await db.storage
.from("videos").upload(path,file,{upsert:false});

if(uploadError)return notice("تعذر رفع الفيديو: "+uploadError.message);

const {data:publicData}=db.storage.from("videos").getPublicUrl(path);

const {error:insertError}=await db.from("videos").insert({
user_id:currentUser.id,
storage_path:path,
caption:$("videoCaption").value.trim(),
visibility:"public"
});

if(insertError){
notice("تعذر حفظ بيانات الفيديو: "+insertError.message);
return;
}

closeModal("uploadModal");
$("videoFile").value="";
$("videoCaption").value="";
notice("تم رفع الفيديو بنجاح");
await loadFeed();
showHome(document.querySelector(".bottom button:last-child"));
}

async function loadFeed(){
const {data,error}=await db.from("videos")
.select("*,profiles(display_name,username,avatar_url)")
.eq("visibility","public")
.order("created_at",{ascending:false})
.limit(50);

if(error){
$("feed").innerHTML="<div class='panel'>تعذر تحميل الفيديوهات: "+
escapeHTML(error.message)+"</div>";
return;
}

videos=data||[];
$("feed").innerHTML="";

if(!videos.length){
$("feed").innerHTML=
"<div class='video-card'><div class='panel'>"+
"<h2>مرحباً بك في WAVO 💚</h2>"+
"<p>عالمك في فيديو واحد.</p>"+
"<p>كن أول من يشارك فيديو مع العالم.</p></div></div>";
return;
}

for(const v of videos){
const {data:urlData}=db.storage.from("videos").getPublicUrl(v.storage_path);
const name=v.profiles?.display_name||v.profiles?.username||"مستخدم WAVO";

const card=document.createElement("article");
card.className="video-card";

const video=document.createElement("video");
video.src=urlData.publicUrl;
video.playsInline=true;
video.loop=true;
video.controls=true;
video.preload="metadata";
video.addEventListener("click",()=>{
if(video.paused)video.play().catch(()=>{});
else video.pause();
});

const info=document.createElement("div");
info.className="video-info";
const title=document.createElement("strong");
title.textContent="@"+name;
const caption=document.createElement("p");
caption.textContent=v.caption||"";
info.append(title,caption);

const actions=document.createElement("div");
actions.className="actions";

const like=document.createElement("button");
like.innerHTML="♡";
const likeCount=document.createElement("small");
const {count:likes}=await db.from("likes")
.select("*",{count:"exact",head:true}).eq("video_id",v.id);
likeCount.textContent=likes||0;
like.onclick=()=>toggleLike(v.id,like,likeCount);

const comment=document.createElement("button");
comment.innerHTML="💬";
comment.onclick=()=>openComments(v.id);

const save=document.createElement("button");
save.innerHTML="🔖";
save.onclick=()=>saveVideo(v.id);

const follow=document.createElement("button");
follow.innerHTML="👤";
follow.onclick=()=>followUser(v.user_id);

const share=document.createElement("button");
share.innerHTML="↗";
share.onclick=()=>shareVideo(urlData.publicUrl);

const makeAction=(button,label)=>{
const wrapper=document.createElement("div");
wrapper.append(button);
const small=document.createElement("small");
small.textContent=label;
wrapper.append(small);
actions.append(wrapper);
};

makeAction(like,likeCount.textContent);
like.append(likeCount);
makeAction(comment,"تعليق");
makeAction(save,"حفظ");
makeAction(follow,"متابعة");
makeAction(share,"مشاركة");

card.append(video,info,actions);
$("feed").append(card);
}
}

async function toggleLike(videoId,button,countElement){
if(!currentUser)return showModal("authModal");

const {data:existing}=await db.from("likes")
.select("id").eq("video_id",videoId)
.eq("user_id",currentUser.id).maybeSingle();

if(existing){
await db.from("likes").delete().eq("id",existing.id);
button.firstChild.textContent="♡";
}else{
const {error}=await db.from("likes").insert({
video_id:videoId,user_id:currentUser.id
});
if(error)return notice(error.message);
button.firstChild.textContent="♥";
}
const {count}=await db.from("likes")
.select("*",{count:"exact",head:true}).eq("video_id",videoId);
countElement.textContent=count||0;
}

async function openComments(videoId){
currentVideo=videoId;
showModal("commentsModal");
$("commentsList").textContent="جارٍ تحميل التعليقات...";
const {data,error}=await db.from("comments")
.select("*,profiles(display_name)")
.eq("video_id",videoId)
.order("created_at",{ascending:true});

if(error)return $("commentsList").textContent=error.message;
$("commentsList").innerHTML="";
(data||[]).forEach(c=>{
const div=document.createElement("div");
div.className="item";
div.textContent=(c.profiles?.display_name||"مستخدم")+": "+c.body;
$("commentsList").append(div);
});
}

async function sendComment(){
if(!currentUser)return showModal("authModal");
const body=$("commentText").value.trim();
if(!body)return notice("اكتب تعليقاً");
const {error}=await db.from("comments").insert({
video_id:currentVideo,user_id:currentUser.id,body
});
if(error)return notice(error.message);
$("commentText").value="";
await openComments(currentVideo);
notice("تم إرسال التعليق");
}

async function saveVideo(videoId){
if(!currentUser)return showModal("authModal");
const {data:existing}=await db.from("saved_videos")
.select("video_id").eq("user_id",currentUser.id)
.eq("video_id",videoId).maybeSingle();

if(existing){
const {error}=await db.from("saved_videos").delete()
.eq("user_id",currentUser.id).eq("video_id",videoId);
if(error)return notice(error.message);
return notice("تمت إزالة الفيديو من المفضلة");
}
const {error}=await db.from("saved_videos").insert({
user_id:currentUser.id,video_id:videoId
});
if(error)return notice(error.message);
notice("تم حفظ الفيديو ❤️");
}

async function followUser(userId){
if(!currentUser)return showModal("authModal");
if(userId===currentUser.id)return notice("هذا حسابك");

const {data:existing}=await db.from("follows")
.select("follower_id")
.eq("follower_id",currentUser.id)
.eq("following_id",userId).maybeSingle();

if(existing){
const {error}=await db.from("follows").delete()
.eq("follower_id",currentUser.id)
.eq("following_id",userId);
if(error)return notice(error.message);
notice("تم إلغاء المتابعة");
}else{
const {error}=await db.from("follows").insert({
follower_id:currentUser.id,following_id:userId
});
if(error)return notice(error.message);
notice("أصبحت تتابع هذا المستخدم");
}
}

async function searchFriends(){
if(!currentUser)return showModal("authModal");
const term=$("friendSearch").value.trim();
if(!term)return;
const {data,error}=await db.from("profiles")
.select("id,username,display_name")
.ilike("username","%"+term+"%").limit(20);

if(error)return notice(error.message);
$("friendResults").innerHTML="";
(data||[]).forEach(p=>{
const row=document.createElement("div");
row.className="item";
const name=document.createElement("span");
name.textContent=p.display_name||p.username;
const btn=document.createElement("button");
btn.className="secondary";
btn.textContent="متابعة";
btn.onclick=()=>followUser(p.id);
row.append(name,btn);
$("friendResults").append(row);
});
}

async function showSaved(){
if(!currentUser)return showModal("authModal");
const {data,error}=await db.from("saved_videos")
.select("video_id,videos(id,caption,storage_path)")
.eq("user_id",currentUser.id);

if(error)return notice(error.message);
$("accountContent").innerHTML="<h3>المفضلة</h3>";
(data||[]).forEach(item=>{
const v=item.videos;
if(!v)return;
const div=document.createElement("div");
div.className="item";
div.textContent=v.caption||"فيديو محفوظ";
$("accountContent").append(div);
});
}

async function showMyVideos(){
if(!currentUser)return showModal("authModal");
const {data,error}=await db.from("videos")
.select("*").eq("user_id",currentUser.id)
.order("created_at",{ascending:false});

if(error)return notice(error.message);
$("accountContent").innerHTML="<h3>فيديوهاتي</h3>";
(data||[]).forEach(v=>{
const div=document.createElement("div");
div.className="item";
div.textContent=v.caption||"فيديو بدون وصف";
$("accountContent").append(div);
});
}

async function loadMessages(){
if(!currentUser)return $("messagesList").textContent="سجل الدخول أولاً";
const {data,error}=await db.from("messages")
.select("*,profiles:sender_id(display_name)")
.or("sender_id.eq."+currentUser.id+",receiver_id.eq."+currentUser.id)
.order("created_at",{ascending:false}).limit(50);

if(error)return $("messagesList").textContent=error.message;
$("messagesList").innerHTML="";
const users=new Set();
(data||[]).forEach(m=>{
const other=m.sender_id===currentUser.id?m.receiver_id:m.sender_id;
if(users.has(other))return;
users.add(other);
const div=document.createElement("div");
div.className="item";
div.textContent="محادثة مع: "+other;
const btn=document.createElement("button");
btn.className="secondary";
btn.textContent="فتح المحادثة";
btn.onclick=()=>openChat(other);
div.append(btn);
$("messagesList").append(div);
});
}

async function openChat(userId){
if(!currentUser)return;
chatUser=userId;
$("chatTitle").textContent="محادثة";
showModal("chatModal");
await refreshChat();
}

async function refreshChat(){
const {data,error}=await db.from("messages")
.select("*")
.or("and(sender_id.eq."+currentUser.id+",receiver_id.eq."+chatUser+"),and(sender_id.eq."+chatUser+",receiver_id.eq."+currentUser.id+")")
.order("created_at",{ascending:true}).limit(100);

if(error)return notice(error.message);
$("chatContent").innerHTML="";
(data||[]).forEach(m=>{
const div=document.createElement("div");
div.className="item";
div.textContent=(m.sender_id===currentUser.id?"أنت: ":"هو: ")+m.body;
$("chatContent").append(div);
});
}

async function sendMessage(){
if(!currentUser||!chatUser)return;
const body=$("chatText").value.trim();
if(!body)return;
const {error}=await db.from("messages").insert({
sender_id:currentUser.id,receiver_id:chatUser,body
});
if(error)return notice(error.message);
$("chatText").value="";
await refreshChat();
}

async function shareVideo(url){
if(navigator.share){
try{await navigator.share({title:"WAVO",url});}catch(e){}
}else{
await navigator.clipboard.writeText(url);
notice("تم نسخ رابط الفيديو");
}
}

function escapeHTML(value){
return String(value).replace(/[&<>"']/g,c=>({
"&":"&amp;","<":"&lt;",">":"&gt;",
'"':"&quot;","'":"&#39;"
}[c]));
}

function changeLanguage(){
const languages=["ar","en","fr"];
language=languages[(languages.indexOf(language)+1)%3];
const labels={
ar:["الرئيسية","الأصدقاء","صندوق الوارد","حسابي"],
en:["Home","Friends","Inbox","Profile"],
fr:["Accueil","Amis","Messages","Profil"]
};
const buttons=document.querySelectorAll(".bottom button");
buttons[0].lastChild.textContent=labels[language][3];
buttons[1].lastChild.textContent=labels[language][2];
buttons[3].lastChild.textContent=labels[language][1];
buttons[4].lastChild.textContent=labels[language][0];
document.documentElement.lang=language;
document.documentElement.dir=language==="ar"?"rtl":"ltr";
notice("Language: "+language.toUpperCase());
}

initialize();
</script>
</body>
</html>
