# Alyssa Haidar

I am an upcoming third year Computer Science and Economics Major.

## My playlist

<iframe data-testid="embed-iframe" style="border-radius:12px" src="https://open.spotify.com/embed/playlist/37i9dQZF1DZ06evO0BkznP?utm_source=generator&theme=0" width="100%" height="352" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>

## Photo Gallery

<style>
  .carousel-section {
    position: relative;
    width: 100vw;
    left: 50%;
    transform: translateX(-50%);
    background: linear-gradient(160deg, #ffe4ee, #fff0f5, #fce4ec);
    padding: 40px 0 50px 0;
    text-align: center;
  }
  .carousel-frame {
    position: relative;
    display: inline-block;
    width: 70%;
    max-width: 700px;
  }
  .carousel-frame img {
    width: 100%;
    height: 420px;
    object-fit: cover;
    border-radius: 16px;
    box-shadow: 0 8px 32px rgba(200,80,120,0.18);
    display: block;
  }
  .carousel-btn {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: rgba(255,255,255,0.9);
    border: none;
    border-radius: 50%;
    width: 44px;
    height: 44px;
    font-size: 22px;
    cursor: pointer;
    box-shadow: 0 2px 8px rgba(0,0,0,0.15);
    color: #e8829a;
    line-height: 1;
  }
  .carousel-btn:hover { background: white; transform: translateY(-50%) scale(1.1); }
  .btn-left  { left:  -22px; }
  .btn-right { right: -22px; }
  .carousel-dots { margin-top: 18px; }
  .carousel-dots span {
    display: inline-block;
    width: 8px; height: 8px;
    border-radius: 50%;
    background: #f5b8cc;
    margin: 0 5px;
    cursor: pointer;
    transition: background 0.2s, transform 0.2s;
  }
  .carousel-dots span.active {
    background: #e8829a;
    transform: scale(1.4);
  }
  .carousel-counter {
    margin-top: 10px;
    color: #e8829a;
    font-size: 13px;
    letter-spacing: 1px;
  }
</style>

<div class="carousel-section">
  <div class="carousel-frame">
    <img id="carousel-img" src="gallery/20260517_150553.jpg" />
    <button class="carousel-btn btn-left"  onclick="prevPhoto()">&#8249;</button>
    <button class="carousel-btn btn-right" onclick="nextPhoto()">&#8250;</button>
  </div>
  <div class="carousel-dots" id="carousel-dots"></div>
  <div class="carousel-counter" id="carousel-counter"></div>
</div>

<script>
const photos = [
  "gallery/",
  
];
let current = 0;

const img     = document.getElementById("carousel-img");
const dotsEl  = document.getElementById("carousel-dots");
const counter = document.getElementById("carousel-counter");

photos.forEach((_, i) => {
  const dot = document.createElement("span");
  if (i === 0) dot.classList.add("active");
  dot.onclick = () => goTo(i);
  dotsEl.appendChild(dot);
});

function goTo(n) {
  current = (n + photos.length) % photos.length;
  img.src = photos[current];
  document.querySelectorAll("#carousel-dots span").forEach((d, i) => {
    d.classList.toggle("active", i === current);
  });
  counter.textContent = (current + 1) + " / " + photos.length;
}
function nextPhoto() { goTo(current + 1); }
function prevPhoto() { goTo(current - 1); }
goTo(0);
</script>