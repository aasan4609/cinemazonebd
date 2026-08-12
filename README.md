<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CinemaZone</title>

<link rel="stylesheet" href="style.css">

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&display=swap" rel="stylesheet">
</head>

<body>

<header>
<div class="logo">CinemaZone</div>

<input type="text" id="search" placeholder="Search Movies...">
</header>

<section id="banner">

<div class="overlay">

<h1 id="banner-title">Loading...</h1>

<p id="banner-desc"></p>

<button>▶ Play</button>

<button>ℹ More Info</button>

</div>

</section>

<section>

<h2>Trending Movies</h2>

<div id="trending" class="row"></div>

</section>

<section>

<h2>Popular Movies</h2>

<div id="popular" class="row"></div>

</section>

<section>

<h2>Top Rated</h2>

<div id="toprated" class="row"></div>

</section>

<script src="script.js"></script>

</body>

</html>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:'Poppins',sans-serif;
}

body{
    background:#141414;
    color:#fff;
}

header{
    position:fixed;
    top:0;
    left:0;
    width:100%;
    padding:15px 50px;
    display:flex;
    justify-content:space-between;
    align-items:center;
    background:rgba(0,0,0,.75);
    z-index:1000;
}

.logo{
    color:#e50914;
    font-size:32px;
    font-weight:700;
}

#search{
    width:260px;
    padding:10px 15px;
    border:none;
    outline:none;
    border-radius:5px;
    background:#222;
    color:#fff;
}

#banner{
    height:85vh;
    background-size:cover;
    background-position:center;
    display:flex;
    align-items:flex-end;
    padding:60px;
    position:relative;
}

#banner::before{
    content:'';
    position:absolute;
    inset:0;
    background:linear-gradient(to top,#141414 10%,transparent 60%);
}

.overlay{
    position:relative;
    max-width:600px;
    z-index:2;
}

.overlay h1{
    font-size:55px;
    margin-bottom:15px;
}

.overlay p{
    line-height:1.6;
    margin-bottom:20px;
    color:#ddd;
}

.overlay button{
    padding:12px 25px;
    border:none;
    border-radius:5px;
    margin-right:10px;
    cursor:pointer;
    font-size:16px;
    font-weight:bold;
}

.overlay button:first-child{
    background:#fff;
    color:#000;
}

.overlay button:last-child{
    background:rgba(109,109,110,.7);
    color:#fff;
}

section{
    padding:20px 40px;
}

section h2{
    margin-bottom:15px;
}

.row{
    display:flex;
    overflow-x:auto;
    gap:15px;
    scroll-behavior:smooth;
    padding-bottom:15px;
}

.row::-webkit-scrollbar{
    display:none;
}

.movie{
    min-width:180px;
    transition:.3s;
    cursor:pointer;
}

.movie img{
    width:100%;
    border-radius:8px;
}

.movie:hover{
    transform:scale(1.08);
}

.movie h3{
    margin-top:8px;
    font-size:15px;
    text-align:center;
}

@media(max-width:768px){

header{
    padding:15px;
}

.logo{
    font-size:24px;
}

#search{
    width:150px;
}

#banner{
    height:65vh;
    padding:20px;
}

.overlay h1{
    font-size:30px;
}

.movie{
    min-width:130px;
}

}
const API_KEY = "YOUR_TMDB_API_KEY";
const IMG = "https://image.tmdb.org/t/p/w500";
const BASE = "https://api.themoviedb.org/3";

const banner = document.getElementById("banner");
const bannerTitle = document.getElementById("banner-title");
const bannerDesc = document.getElementById("banner-desc");

async function loadRow(type, id) {
    const res = await fetch(`${BASE}/movie/${type}?api_key=${API_KEY}`);
    const data = await res.json();

    const row = document.getElementById(id);
    row.innerHTML = "";

    data.results.forEach(movie => {
        if (!movie.poster_path) return;

        row.innerHTML += `
        <div class="movie">
            <img src="${IMG}${movie.poster_path}" alt="${movie.title}">
            <h3>${movie.title}</h3>
        </div>`;
    });

    if (id === "trending") {
        const random = data.results[Math.floor(Math.random() * data.results.length)];

        banner.style.backgroundImage =
            `url(https://image.tmdb.org/t/p/original${random.backdrop_path})`;

        bannerTitle.textContent = random.title;
        bannerDesc.textContent = random.overview;
    }
}

loadRow("popular", "popular");
loadRow("top_rated", "toprated");

// Trending (TMDb Trending API)
fetch(`${BASE}/trending/movie/week?api_key=${API_KEY}`)
.then(res => res.json())
.then(data => {
    const row = document.getElementById("trending");

    data.results.forEach(movie => {
        if (!movie.poster_path) return;

        row.innerHTML += `
        <div class="movie">
            <img src="${IMG}${movie.poster_path}" alt="${movie.title}">
            <h3>${movie.title}</h3>
        </div>`;
    });

    const random = data.results[Math.floor(Math.random() * data.results.length)];

    banner.style.backgroundImage =
        `url(https://image.tmdb.org/t/p/original${random.backdrop_path})`;

    bannerTitle.textContent = random.title;
    bannerDesc.textContent = random.overview;
});

// Search
document.getElementById("search").addEventListener("keyup", async (e) => {
    const q = e.target.value.trim();
    if (q.length < 2) return;

    const res = await fetch(
        `${BASE}/search/movie?api_key=${API_KEY}&query=${encodeURIComponent(q)}`
    );
    const data = await res.json();

    const row = document.getElementById("trending");
    row.innerHTML = "";

    data.results.forEach(movie => {
        if (!movie.poster_path) return;

        row.innerHTML += `
        <div class="movie">
            <img src="${IMG}${movie.poster_path}" alt="${movie.title}">
            <h3>${movie.title}</h3>
        </div>`;
    });
});
