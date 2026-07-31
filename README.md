<!DOCTYPE html>
<html>
  <body>
  <h1>Welcome to my OS
  <h2>Introduction</h2>
  <p>Hello world</p>
  </body>
  <body>
  (rest of your content)
  <a href="https://stardance.hackclub.com/amd">label</a>
</body>
<body style="background-color: #c9ffcf">
  // your content//
</body>
<h1>我的相册</h1>
<div class="album">
    <img src="1.jpg" onclick="showBig(this.src)">
    <img src="2.jpg" onclick="showBig(this.src)">
    <img src="3.jpg" onclick="showBig(this.src)">
    <img src="4.jpg" onclick="showBig(this.src)">
    <img src="5.jpg" onclick="showBig(this.src)">
    <img src="6.jpg" onclick="showBig(this.src)">
</div>

<div class="mask" id="mask">
    <span class="close" onclick="closeBig()">×</span>
    <img src="" id="bigImg">
</div>

<style>
.album{
    display: grid;
    grid-template-columns: repeat(3,1fr);
    gap: 15px;
    padding: 20px;
}
.album img{
    width: 100%;
    height: 180px;
    object-fit: cover;
    border-radius: 8px;
    cursor: pointer;
    transition: 0.2s;
}
.album img:hover{
    transform: scale(1.03);
}
.mask{
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0,0,0,0.85);
    display: none;
    justify-content: center;
    align-items: center;
}
.mask img{
    max-width: 90%;
    max-height: 90vh;
    border-radius: 8px;
}
.close{
    position: absolute;
    top: 20px;
    right: 30px;
    color: white;
    font-size: 40px;
    cursor: pointer;
}
</style>

<script>
function showBig(src){
    document.getElementById("bigImg").src = src;
    document.getElementById("mask").style.display = "flex";
}
function closeBig(){
    document.getElementById("mask").style.display = "none";
}
</script>
<div id=c style="position:fixed;top:10px;right:10px;background:#000;color:#fff;padding:8px 16px;border-radius:20px;font:18px monospace;z-index:999;box-shadow:0 4px 12px rgba(0,0,0,.3)"></div>
<script>
c.innerText = new Date().toLocaleString('zh-CN', { hour12: false });
setInterval(() => c.innerText = new Date().toLocaleString('zh-CN', { hour12: false }), 1000);
</script>
</html>
