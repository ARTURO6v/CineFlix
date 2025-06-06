# CineFlix
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>CineFlix</title>
  <style>
    /* Reset básico */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background-color: #141414;
      color: #fff;
      font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }

    /* Header tipo Netflix */
    header {
      background-color: #000;
      padding: 1rem 2rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
      gap: 1rem;
      position: sticky;
      top: 0;
      z-index: 100;
    }

    .logo {
      font-size: 2rem;
      font-weight: bold;
      color: #e50914;
      user-select: none;
    }

    .logo span {
      color: white;
    }

    header input, header select {
      padding: 0.5rem 1rem;
      font-size: 1rem;
      border-radius: 5px;
      border: none;
      background-color: #333;
      color: white;
      min-width: 150px;
    }

    /* Secciones */
    .seccion {
      padding: 2rem;
      flex-grow: 1;
    }

    .seccion h2 {
      margin-bottom: 1rem;
      font-weight: 600;
      font-size: 1.5rem;
    }

    /* Estilo tipo carrusel */
    .movie-row {
      display: flex;
      gap: 1rem;
      overflow-x: auto;
      padding-bottom: 1rem;
      scrollbar-width: thin;
      scrollbar-color: #e50914 transparent;
    }

    .movie-row::-webkit-scrollbar {
      height: 8px;
    }

    .movie-row::-webkit-scrollbar-track {
      background: transparent;
    }

    .movie-row::-webkit-scrollbar-thumb {
      background-color: #e50914;
      border-radius: 10px;
    }

    .movie-card {
      min-width: 200px;
      background-color: #222;
      border-radius: 10px;
      overflow: hidden;
      transition: transform 0.2s ease;
      flex-shrink: 0;
      cursor: pointer;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);
      display: flex;
      flex-direction: column;
      height: 360px;
    }

    .movie-card:hover {
      transform: scale(1.07);
      box-shadow: 0 0 15px #e50914;
    }

    .movie-card img {
      width: 100%;
      height: 300px;
      object-fit: cover;
      flex-shrink: 0;
    }

    .movie-info {
      padding: 0.6rem 0.8rem;
      flex-grow: 1;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }

    .movie-info h3 {
      font-size: 1rem;
      margin-bottom: 0.3rem;
      line-height: 1.2;
      flex-grow: 1;
    }

    .movie-info button {
      background-color: #e50914;
      border: none;
      color: white;
      padding: 0.5rem;
      width: 100%;
      border-radius: 5px;
      cursor: pointer;
      font-weight: 600;
      font-size: 0.9rem;
      transition: background-color 0.3s ease;
    }

    .movie-info button:hover {
      background-color: #b0060e;
    }

    /* Responsive */
    @media (max-width: 600px) {
      header {
        flex-direction: column;
        align-items: flex-start;
      }

      header input, header select {
        width: 100%;
        min-width: unset;
      }

      .movie-card {
        min-width: 150px;
        height: 280px;
      }

      .movie-card img {
        height: 220px;
      }
    }
  </style>
</head>
<body>
  <header>
    <div class="logo">Cine<span>Flix</span></div>
    <input type="text" id="buscador" placeholder="Buscar película..." autocomplete="off" />
    <select id="genero" aria-label="Filtrar por género">
      <option value="todos">Todos</option>
      <option value="Acción">Acción</option>
      <option value="Ciencia ficción">Ciencia ficción</option>
      <option value="Drama">Drama</option>
      <option value="Comedia">Comedia</option>
      <option value="Histórica">Histórica</option>
    </select>
  </header>

  <section class="seccion">
    <h2>🎥 Cartelera destacada</h2>
    <div class="movie-row" id="peliculas" aria-live="polite" aria-label="Listado de películas"></div>
  </section>

  <script>
    // Datos de películas embebidos (sin fetch)
    const peliculas = [
      {
        id: 1,
        titulo: "Oppenheimer",
        genero: ["Drama", "Histórica"],
        descripcion: "Biografía de J. Robert Oppenheimer y el desarrollo de la bomba atómica.",
        imagen: "https://i.imgur.com/54YkM0H.jpg", // Puedes cambiar por tus imágenes
        trailer: "https://www.youtube.com/watch?v=8bXcWBc9D0Y"
      },
      {
        id: 2,
        titulo: "Godzilla Minus One",
        genero: ["Acción", "Ciencia ficción"],
        descripcion: "Godzilla ataca el Japón de posguerra.",
        imagen: "https://i.imgur.com/CSXPRx3.jpg",
        trailer: "https://www.youtube.com/watch?v=Kng9P1R8TZM"
      },
      {
        id: 3,
        titulo: "Spider-Man: No Way Home",
        genero: ["Acción", "Aventura"],
        descripcion: "Peter Parker enfrenta consecuencias en multiversos.",
        imagen: "https://i.imgur.com/tM5NDs7.jpg",
        trailer: "https://www.youtube.com/watch?v=JfVOs4VSpmA"
      },
      {
        id: 4,
        titulo: "Dune: Parte Dos",
        genero: ["Ciencia ficción", "Acción"],
        descripcion: "La continuación épica de Dune.",
        imagen: "https://i.imgur.com/OMTTrvO.jpg",
        trailer: "https://www.youtube.com/watch?v=U2nF1gC8Vyc"
      }
    ];

    const contenedor = document.getElementById('peliculas');
    const buscador = document.getElementById('buscador');
    const generoSelect = document.getElementById('genero');

    // Función para mostrar películas en pantalla
    function mostrarPeliculas(lista) {
      contenedor.innerHTML = '';
      if(lista.length === 0) {
        contenedor.innerHTML = '<p style="color:#bbb; padding:1rem;">No se encontraron películas.</p>';
        return;
      }
      lista.forEach(pelicula => {
        const div = document.createElement('div');
        div.className = 'movie-card';
        div.setAttribute('tabindex', '0');
        div.innerHTML = `
          <img src="${pelicula.imagen}" alt="Poster de ${pelicula.titulo}" loading="lazy" />
          <div class="movie-info">
            <h3>${pelicula.titulo}</h3>
            <button aria-label="Ver detalles de ${pelicula.titulo}" onclick="verDetalles(${pelicula.id})">Ver</button>
          </div>
        `;
        contenedor.appendChild(div);
      });
    }

    // Función para filtrar según búsqueda y género
    function filtrarPeliculas() {
      const texto = buscador.value.toLowerCase();
      const genero = generoSelect.value;
      const filtradas = peliculas.filter(p => {
        const cumpleTexto = p.titulo.toLowerCase().includes(texto);
        const cumpleGenero = genero === 'todos' || p.genero.includes(genero);
        return cumpleTexto && cumpleGenero;
      });
      mostrarPeliculas(filtradas);
    }

    // Evento de búsqueda
    buscador.addEventListener('input', filtrarPeliculas);

    // Evento de filtro por género
    generoSelect.addEventListener('change', filtrarPeliculas);

    // Función para ir a la página de detalles (puedes crear movie.html y manejar id)
    function verDetalles(id) {
      alert("Aquí podrías ir a la página de detalles de la película con id: " + id);
      // Por ejemplo: window.location.href = `movie.html?id=${id}`;
    }

    // Mostrar todas al cargar
    document.addEventListener('DOMContentLoaded', () => {
      mostrarPeliculas(peliculas);
    });
  </script>
</body>
</html>
