<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spotify Web Player</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700&display=swap" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">
    <style>
        /* General Styles */
        :root {
            --spotify-green: #1db954;
            --dark-gray: #121212;
            --medium-gray: #282828;
            --light-gray: #b3b3b3;
            --white: #ffffff;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            background-color: var(--dark-gray);
            color: var(--light-gray);
            font-family: 'Montserrat', sans-serif;
            font-size: 14px;
        }

        a {
            text-decoration: none;
            color: var(--light-gray);
        }

        /* App Layout with CSS Grid */
        .app-container {
            display: grid;
            grid-template-areas:
                "sidebar main-content"
                "player player";
            grid-template-columns: 250px 1fr;
            grid-template-rows: 1fr 90px;
            height: 100vh;
            overflow: hidden;
            background: linear-gradient(to bottom, #1f1f1f, #121212);
        }

        .sidebar {
            grid-area: sidebar;
            background-color: #000;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .main-content {
            grid-area: main-content;
            background-color: var(--medium-gray);
            overflow-y: auto;
            position: relative;
            padding: 20px;
            animation: fadeIn 0.5s ease-in-out;
        }

        .player-bar {
            grid-area: player;
            background-color: #181818;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;
        }

        /* Sidebar Styles */
        .logo {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 20px;
        }

        .logo i {
            font-size: 40px;
            color: var(--spotify-green);
        }

        .logo span {
            font-size: 24px;
            font-weight: bold;
            color: var(--white);
        }

        .main-nav ul, .playlists ul {
            list-style: none;
        }

        .main-nav li a, .playlists li a {
            display: flex;
            align-items: center;
            gap: 15px;
            padding: 10px 15px;
            border-radius: 5px;
            transition: background-color 0.2s, color 0.2s;
        }

        .main-nav li a:hover, .playlists li a:hover {
            background-color: var(--medium-gray);
            color: var(--white);
        }

        .main-nav li a i, .playlists li a i {
            font-size: 20px;
        }

        .playlists {
            flex-grow: 1;
            padding-top: 20px;
            border-top: 1px solid #282828;
        }
        
        .playlists h4 {
            margin-bottom: 10px;
            color: var(--white);
        }
        
        .user-playlists li a {
            padding: 5px 15px;
        }

        /* Top Bar Styles */
        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
            position: sticky;
            top: 0;
            background-color: rgba(40, 40, 40, 0.8);
            backdrop-filter: blur(10px);
            z-index: 10;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        .navigation-buttons .nav-btn {
            background-color: rgba(0, 0, 0, 0.5);
            border: none;
            border-radius: 50%;
            width: 35px;
            height: 35px;
            color: var(--white);
            cursor: pointer;
            margin-right: 10px;
            transition: background-color 0.2s;
        }

        .navigation-buttons .nav-btn:hover {
            background-color: rgba(0, 0, 0, 0.8);
        }

        .user-profile {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .upgrade-btn {
            background: linear-gradient(to right, #1ed760, #1db954);
            border: none;
            color: var(--white);
            padding: 10px 20px;
            border-radius: 50px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .upgrade-btn:hover {
            transform: scale(1.05);
        }

        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            cursor: pointer;
        }

        /* Content Section */
        .content-section {
            padding-bottom: 100px; /* Space for the player bar */
        }
        
        h2 {
            color: var(--white);
            margin-bottom: 20px;
        }

        .grid-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
        }

        .card {
            background-color: #181818;
            border-radius: 8px;
            padding: 20px;
            text-align: center;
            transition: background-color 0.3s, transform 0.3s;
            cursor: pointer;
        }

        .card:hover {
            background-color: #282828;
            transform: translateY(-5px);
        }

        .card img {
            width: 100%;
            border-radius: 5px;
            margin-bottom: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }
        
        .card h4 {
            color: var(--white);
            margin-bottom: 5px;
        }

        .card p {
            font-size: 12px;
            color: var(--light-gray);
        }

        /* Animations */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes spin {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        .loading-spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid var(--spotify-green);
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 50px auto;
        }
    </style>
</head>
<body>

    <div class="app-container">

        <aside class="sidebar">
            <div class="logo">
                <i class="fab fa-spotify"></i>
                <span>Spotify</span>
            </div>
            <nav class="main-nav">
                <ul>
                    <li><a href="#" data-section="home"><i class="fas fa-home"></i> Home</a></li>
                    <li><a href="#" data-section="search"><i class="fas fa-search"></i> Search</a></li>
                    <li><a href="#" data-section="library"><i class="fas fa-grip-lines"></i> Your Library</a></li>
                </ul>
            </nav>
            <div class="playlists">
                <h4>Playlists</h4>
                <ul>
                    <li><a href="#"><i class="fas fa-plus"></i> Create Playlist</a></li>
                    <li><a href="#"><i class="fas fa-heart"></i> Liked Songs</a></li>
                </ul>
                <hr style="border: none; border-top: 1px solid #282828; margin: 10px 0;">
                <ul class="user-playlists">
                    <li><a href="#">Chill Vibes</a></li>
                    <li><a href="#">Workout Mix</a></li>
                    <li><a href="#">Study Session</a></li>
                </ul>
            </div>
        </aside>

        <main class="main-content">
            <header class="top-bar">
                <div class="navigation-buttons">
                    <button class="nav-btn"><i class="fas fa-chevron-left"></i></button>
                    <button class="nav-btn"><i class="fas fa-chevron-right"></i></button>
                </div>
                <div class="user-profile">
                    <button class="upgrade-btn">Upgrade</button>
                    <img src="https://i.pravatar.cc/150?img=3" alt="User Avatar" class="user-avatar">
                </div>
            </header>

            <section class="content-section">
                </section>
        </main>

        <footer class="player-bar">
            <div style="color: white;">Music Player Coming Soon!</div>
        </footer>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const contentSection = document.querySelector('.content-section');
            const navLinks = document.querySelectorAll('.main-nav a, .playlists a');

            // Function to fetch and display content based on section
            function loadContent(section) {
                contentSection.innerHTML = '<div class="loading-spinner"></div>'; // Show a spinner
                
                // Simulate an API call with a delay
                setTimeout(() => {
                    let html = '';
                    switch (section) {
                        case 'home':
                            html = `
                                <h2>Good evening</h2>
                                <div class="grid-container">
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRMj4ZHqebrmPA4ff-TDG3FCuvrCNQV3rhOZg&s" alt="Album 1">
                                        <h4>Album Title 1</h4>
                                        <p>Artist Name</p>
                                    </div>
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSFAz-lYc-hVgiTCxcXtnPZLEi1I1Kg23FebQ&s" alt="Album 2">
                                        <h4>Album Title 2</h4>
                                        <p>Artist Name</p>
                                    </div>
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSPlppyvj9aVRDyMfGibylZ_gT2rtIHIPaO9w&s" alt="Album 3">
                                        <h4>Album Title 3</h4>
                                        <p>Artist Name</p>
                                    </div>
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTPUAG7HR1xY67AUv1zPeLEh3RYNmmSQtqsBw&s" alt="Album 4">
                                        <h4>Album Title 4</h4>
                                        <p>Artist Name</p>
                                    </div>
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR9wyCSdnDHY6A7AhHv7eE-rJJIWEQGaXj_AA&s" alt="Album 5">
                                        <h4>Album Title 5</h4>
                                        <p>Artist Name</p>
                                    </div>
                                    <div class="card">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSsAESTSS_HXeuaIStX3DyOcCYrC4P9oQxygw&s" alt="Album 6">
                                        <h4>Album Title 6</h4>
                                        <p>Artist Name</p>
                                    </div>
                                </div>
                            `;
                            break;
                        case 'search':
                            html = '<h2>Search</h2><p>Search functionality goes here...</p>';
                            break;
                        case 'library':
                            html = '<h2>Your Library</h2><p>Your saved songs and playlists will appear here...</p>';
                            break;
                        default:
                            html = '<h2>Page Not Found</h2><p>The content you are looking for does not exist.</p>';
                    }
                    contentSection.innerHTML = html;
                }, 800);
            }
            
            // Event listeners for navigation links
            navLinks.forEach(link => {
                link.addEventListener('click', (e) => {
                    e.preventDefault();
                    const section = link.getAttribute('data-section');
                    if (section) {
                        loadContent(section);
                    }
                });
            });

            // Load the home content by default
            loadContent('home');
        });
    </script>
</body>
</html>
