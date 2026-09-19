<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pixora - موقع نشر الصور</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root{--bg:#0a0a0a;--card:#18181b;--border:#27272a;--red:#ff2442;--text:#fafafa;--muted:#a1a1aa}
*{margin:0;padding:0;box-sizing:border-box;font-family:'Segoe UI',system-ui}
body{background:var(--bg);color:var(--text);min-height:100vh}
header{
  position:sticky;top:0;z-index:100;
  background:rgba(10,10,10,.9);backdrop-filter:blur(20px);
  border-bottom:1px solid var(--border);
  display:flex;align-items:center;justify-content:space-between;
  padding:12px 20px;
}
.logo{font-weight:900;font-size:24px;letter-spacing:-1px}
.logo span{color:var(--red)}
.search{flex:1;max-width:500px;margin:0 20px;position:relative}
.search i{position:absolute;right:15px;top:50%;transform:translateY(-50%);color:var(--muted)}
.search input{width:100%;background:var(--card);border:1px solid var(--border);padding:12px 42px 12px 12px;border-radius:99px;color:#fff;outline:none}
.search input:focus{border-color:var(--red)}
.nav{display:flex;gap:10px;align-items:center}
.btn{padding:10px 18px;border-radius:99px;border:1px solid var(--border);background:var(--card);color:#fff;cursor:pointer;transition:.2s;font-weight:600}
.btn:hover{transform:translateY(-1px)}
.btn-primary{background:var(--red);border-color:var(--red)}
.btn-primary:hover{box-shadow:0 5px 20px rgba(255,36,66,.4)}
.btn-icon{width:40px;height:40px;display:grid;place-items:center;border-radius:50%}
main{padding:20px;max-width:1400px;margin:auto}
.filters{display:flex;gap:10px;overflow:auto;padding:10px 0 20px;scrollbar-width:none}
.filters::-webkit-scrollbar{display:none}
.filter{white-space:nowrap;padding:8px 16px;background:var(--card);border:1px solid var(--border);border-radius:99px;cursor:pointer;font-size:13px}
.filter.active{background:#fff;color:#000}
.masonry{columns:3 280px;column-gap:16px}
@media(max-width:700px){.masonry{columns:2 150px}.search{display:none}}
.card{
  break-inside:avoid;margin-bottom:16px;background:var(--card);
  border-radius:18px;overflow:hidden;border:1px solid var(--border);
  position:relative;group: card;
}
.card img{width:100%;display:block;transition:.4s}
.card:hover img{transform:scale(1.03);filter:brightness(.7)}
.overlay{
  position:absolute;inset:0;padding:12px;display:flex;
  flex-direction:column;justify-content:space-between;
  opacity:0;transition:.3s;background:linear-gradient(to top, rgba(0,0,0,.8), transparent 50%)
}
.card:hover.overlay{opacity:1}
.top{display:flex;justify-content:space-between}
.bottom{display:flex;justify-content:space-between;align-items:center}
.user{display:flex;align-items:center;gap:8px;font-size:13px}
.user img{width:28px;height:28px;border-radius:50%}
.actions{display:flex;gap:6px}
.actions button{width:32px;height:32px;border-radius:50%;border:none;cursor:pointer;background:rgba(255,255,255,.15);backdrop-filter:blur(10px);color:#fff}
.actions button.liked{background:var(--red)}
.caption{padding:12px;font-size:13px;color:var(--muted)}
.caption b{color:#fff}
/* Upload Modal */
#modal{position:fixed;inset:0;background:rgba(0,0,0,.8);backdrop-filter:blur(10px);z-index:200;display:none;place-items:center;padding:20px}
#modal.show{display:grid}
.modal-box{background:#18181b;border:1px solid var(--border);width:100%;max-width:520px;border-radius:20px;padding:24px;animation:pop.3s}
@keyframes pop{from{transform:scale(.9);opacity:0}to{transform:scale(1);opacity:1}}
.drop{border:2px dashed #333;border-radius:16px;padding:30px;text-align:center;margin:15px 0;cursor:pointer;transition:.2s}
.drop:hover{border-color:var(--red);background:rgba(255,36,66,.05)}
.drop i{font-size:32px;color:var(--red);margin-bottom:10px}
.modal-box input,.modal-box textarea{width:100%;background:#27272a;border:1px solid #3f3f46;padding:12px;border-radius:10px;color:#fff;margin-top:10px;outline:none}
.modal-box textarea{resize:none;height:80px}
</style>
</head>
<body>

<header>
  <div class="logo"><i class="fa-solid fa-camera-retro"></i> Pix<span>ora</span></div>
  <div class="search"><i class="fa-solid fa-magnifying-glass"></i><input id="searchInput" placeholder="ابحث عن صور، اشخاص، هاشتاجات..." oninput="filterSearch()"></div>
  <div class="nav">
    <button class="btn btn-icon"><i class="fa-regular fa-heart"></i></button>
    <button class="btn btn-primary" onclick="openModal()"><i class="fa-solid fa-plus"></i> نشر</button>
    <img src="https://i.pravatar.cc/100" style="width:36px;height:36px;border-radius:50%;border:2px solid var(--red)">
  </div>
</header>

<main>
  <div class="filters">
    <div class="filter active" onclick="setFilter(this,'all')">الكل</div>
    <div class="filter" onclick="setFilter(this,'nature')">طبيعة ⛰️</div>
    <div class="filter" onclick="setFilter(this,'cars')">سيارات 🏎️</div>
    <div class="filter" onclick="setFilter(this,'anime')">انمي 🔥</div>
    <div class="filter" onclick="setFilter(this,'tech')">تقنية 💻</div>
    <div class="filter" onclick="setFilter(this,'girls')">ستايل ✨</div>
  </div>
  <div class="masonry" id="feed"></div>
</main>

<div id="modal">
  <div class="modal-box">
    <div style="display:flex;justify-content:space-between;align-items:center"><h3>نشر صورة جديدة</h3><button class="btn btn-icon" onclick="closeModal()"><i class="fa-solid fa-xmark"></i></button></div>
    <div class="drop" onclick="document.getElementById('file').click()">
      <i class="fa-solid fa-cloud-arrow-up"></i><p>اضغط لرفع الصورة</p><small style="color:var(--muted)">PNG, JPG حتى 10MB</small>
      <img id="preview" style="width:100%;border-radius:12px;margin-top:15px;display:none">
    </div>
    <input type="file" id="file" accept="image/*" hidden onchange="previewImg(event)">
    <input id="title" placeholder="عنوان الصورة... مثلا: غروب الشمس في اسكندرية">
    <textarea id="tags" placeholder="#طبيعة #تصويري"></textarea>
    <select id="cat" style="width:100%;background:#27272a;border:1px solid #3f3f46;padding:12px;border-radius:10px;color:#fff;margin-top:10px"><option value="nature">طبيعة</option><option value="cars">سيارات</option><option value="anime">انمي</option><option value="tech">تقنية</option><option value="girls">ستايل</option></select>
    <button class="btn btn-primary" style="width:100%;margin-top:15px;padding:14px" onclick="publish()">نشر الان <i class="fa-solid fa-paper-plane"></i></button>
  </div>
</div>

<script>
let posts = JSON.parse(localStorage.getItem('pixora_posts')) || [
  {img:'https://images.unsplash.com/photo-1506744038136-46273834b3fb', title:'جبل في الصباح', tags:'#طبيعة #جبال', cat:'nature', likes:124, liked:false},
  {img:'https://images.unsplash.com/photo-1494976388531-d1058494cdd8', title:'سيارتي الجديدة', tags:'#cars #تصوير', cat:'cars', likes:89, liked:true},
  {img:'https://images.unsplash.com/photo-1578662996442-48f60103fc96', title:'ناروتو 🔥', tags:'#anime #naruto', cat:'anime', likes:542, liked:false},
  {img:'https://images.unsplash.com/photo-1517336714731-489689fd1ca8', title:'سيت اب المبرمج', tags:'#tech #setup', cat:'tech', likes:201, liked:false},
  {img:'https://images.unsplash.com/photo-1531746020798-e6953c6e8e04', title:'اوتفيت اليوم', tags:'#style #fashion', cat:'girls', likes:320, liked:false},
  {img:'https://images.unsplash.com/photo-1501785888041-af3ef285b470', title:'بحيرة هادية', tags:'#nature', cat:'nature', likes:76, liked:false},
];

const feed = document.getElementById('feed');
function render(list){
  feed.innerHTML='';
  list.forEach((p,i)=>{
    feed.innerHTML+=`
    <div class="card" data-cat="${p.cat}">
      <img src="${p.img}" loading="lazy">
      <div class="overlay">
        <div class="top"><div class="user"><img src="https://i.pravatar.cc/100?img=${i+5}"><span>ahmed</span></div><button class="btn btn-primary" style="padding:6px 12px;font-size:12px">متابعة</button></div>
        <div class="bottom"><div class="actions"><button onclick="like(${i})" class="${p.liked?'liked':''}"><i class="${p.liked?'fa-solid':'fa-regular'} fa-heart"></i></button><button><i class="fa-solid fa-arrow-up-from-bracket"></i></button></div><span style="font-size:12px"><i class="fa-solid fa-heart"></i> ${p.likes}</span></div>
      </div>
      <div class="caption"><b>${p.title}</b> <br>${p.tags}</div>
    </div>`;
  });
}
render(posts);

function openModal(){document.getElementById('modal').classList.add('show')}
function closeModal(){document.getElementById('modal').classList.remove('show')}
let tempImg='';
function previewImg(e){
  const file=e.target.files[0];
  const reader=new FileReader();
  reader.onload=()=>{
    tempImg=reader.result;
    document.getElementById('preview').src=tempImg;
    document.getElementById('preview').style.display='block';
  }
  reader.readAsDataURL(file);
}
function publish(){
  const title=document.getElementById('title').value || 'بدون عنوان';
  const tags=document.getElementById('tags').value || '#جديد';
  const cat=document.getElementById('cat').value;
  if(!tempImg){alert('ارفع صورة الاول!');return}
  posts.unshift({img:tempImg,title,tags,cat,likes:0,liked:false});
  localStorage.setItem('pixora_posts', JSON.stringify(posts));
  render(posts);closeModal();tempImg='';
  document.getElementById('preview').style.display='none';
  document.getElementById('title').value='';document.getElementById('tags').value='';
}
function like(i){
  posts[i].liked=!posts[i].liked;
  posts[i].likes+=posts[i].liked?1:-1;
  localStorage.setItem('pixora_posts', JSON.stringify(posts));
  render(filtered());
}
function setFilter(el,cat){
  document.querySelectorAll('.filter').forEach(f=>f.classList.remove('active'));
  el.classList.add('active');
  if(cat=='all') render(posts);
  else render(posts.filter(p=>p.cat==cat));
}
function filtered(){
  const active=document.querySelector('.filter.active').textContent;
  // simple return current view
  return posts;
}
function filterSearch(){
  const q=document.getElementById('searchInput').value.toLowerCase();
  render(posts.filter(p=>p.title.toLowerCase().includes(q) || p.tags.toLowerCase().includes(q)));
}
</script>
</body>
</html>
