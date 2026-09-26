# stunning-octo-doodle
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Movie Diary</title>

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #141414;
            color: white;
            margin: 0;
            padding: 20px;
        }

        header {
            text-align: center;
            padding: 20px;
        }

        h1 {
            color: #e50914;
        }

        .form-box {
            background: #252525;
            padding: 20px;
            border-radius: 12px;
            max-width: 500px;
            margin: 20px auto;
        }

        input, select, textarea {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            box-sizing: border-box;
            border-radius: 6px;
            border: none;
            font-size: 15px;
        }

        button {
            background: #e50914;
            color: white;
            border: none;
            padding: 12px 18px;
            border-radius: 6px;
            cursor: pointer;
            margin-top: 8px;
        }

        button:hover {
            background: #b20710;
        }

        #search {
            max-width: 500px;
            display: block;
            margin: 20px auto;
        }

        #movieList {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
            gap: 15px;
        }

        .movie {
            background: #252525;
            padding: 18px;
            border-radius: 10px;
        }

        .movie h3 {
            color: #ffcc00;
        }

        .delete {
            background: #555;
        }

        .empty {
            text-align: center;
            color: #bbb;
        }
    </style>
</head>

<body>

    <header>
        <h1>🎬 My Movie Diary</h1>
        <p>Keep track of every movie you've watched.</p>
    </header>

    <div class="form-box">
        <h2>Add a Movie</h2>

        <form id="movieForm">
            <input id="title" type="text"
                placeholder="Movie title" required>

            <input id="genre" type="text"
                placeholder="Genre (e.g. Romance, Action)" required>

            <input id="year" type="number"
                placeholder="Year released">

            <label for="date">Date watched</label>
            <input id="date" type="date" required>

            <label for="rating">Your rating</label>
            <select id="rating" required>
                <option value="5">★★★★★ - 5 stars</option>
                <option value="4">★★★★☆ - 4 stars</option>
                <option value="3">★★★☆☆ - 3 stars</option>
                <option value="2">★★☆☆☆ - 2 stars</option>
                <option value="1">★☆☆☆☆ - 1 star</option>
            </select>

            <textarea id="review"
                placeholder="Write your review..."
                rows="4"></textarea>

            <button type="submit">Add Movie</button>
        </form>
    </div>

    <input id="search" type="text"
        placeholder="🔍 Search your movies...">

    <h2>My Watched Movies</h2>
    <p id="count">Movies watched: 0</p>

    <div id="movieList"></div>

    <script>
        let movies = JSON.parse(
            localStorage.getItem("myMovies") || "[]"
        );

        const form = document.getElementById("movieForm");
        const movieList = document.getElementById("movieList");
        const search = document.getElementById("search");

        function saveMovies() {
            localStorage.setItem("myMovies", JSON.stringify(movies));
        }

        function renderMovies() {
            movieList.innerHTML = "";

            const query = search.value.toLowerCase();

            const filtered = movies.filter(movie =>
                movie.title.toLowerCase().includes(query) ||
                movie.genre.toLowerCase().includes(query)
            );

            document.getElementById("count").textContent =
                "Movies watched: " + movies.length;

            if (filtered.length === 0) {
                movieList.innerHTML =
                    '<p class="empty">No movies found. Add your first movie!</p>';
                return;
            }

            filtered.forEach(movie => {
                const card = document.createElement("div");
                card.className = "movie";

                const title = document.createElement("h3");
                title.textContent = movie.title;

                const details = document.createElement("p");
                details.textContent =
                    movie.genre + " | " + (movie.year || "Year unknown");

                const rating = document.createElement("p");
                rating.textContent =
                    "⭐".repeat(Number(movie.rating));

                const date = document.createElement("p");
                date.textContent = "Watched: " + movie.date;

                const review = document.createElement("p");
                review.textContent = movie.review || "No review added.";

                const del = document.createElement("button");
                del.textContent = "Delete";
                del.className = "delete";

                del.onclick = () => {
                    movies = movies.filter(m => m.id !== movie.id);
                    saveMovies();
                    renderMovies();
                };

                card.append(title, details, rating, date, review, del);
                movieList.appendChild(card);
            });
        }

        form.addEventListener("submit", function(event) {
            event.preventDefault();

            const movie = {
                id: Date.now(),
                title: document.getElementById("title").value.trim(),
                genre: document.getElementById("genre").value.trim(),
                year: document.getElementById("year").value,
                date: document.getElementById("date").value,
                rating: document.getElementById("rating").value,
                review: document.getElementById("review").value.trim()
            };

            movies.unshift(movie);
            saveMovies();
            renderMovies();

            form.reset();
        });

        search.addEventListener("input", renderMovies);

        renderMovies();
    </script>

</body>
</html>
