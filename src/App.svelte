<script>
  import axios from 'axios';
  import { onMount } from 'svelte';

  const API_URL = 'http://localhost:8080';

  let estantes = [];
  let descripcion = '';
  let ubicacion = '';
  let id_estante = null;

  const updateEstantes = async () => {
    const response = await axios.get(`${API_URL}/estantes`);
    estantes = response.data;
  };

  const addEstante = async () => {
    const data = { descripcion, ubicacion };
    await axios.post(`${API_URL}/estantes`, data);
    updateEstantes();
    clearForm();
  };

  const deleteEstante = async (id) => {
    await axios.delete(`${API_URL}/estantes/${id}`);
    updateEstantes();
  };

  const editEstante = async () => {
    const data = { descripcion, ubicacion };
    await axios.put(`${API_URL}/estantes/${idestante}`, data);
    updateEstantes();
    clearForm();
  };

  const getEstanteById = async (id) => {
    const response = await axios.get(`${API_URL}/estantes/${id}`);
    const estante = response.data;
    descripcion = estante.descripcion;
    ubicacion = estante.ubicacion;
    id_estante = estante.id_estante;
  };

  const clearForm = () => {
    descripcion = '';
    ubicacion = '';
    id_estante = null;
  }

  // Call updateEstates when the component is mounted
  updateEstantes();

  let volumenes = []
  let titulo = ''
  let descripcion_estante = ''
  let ubicacion_estante = ''
  let id_sala = 0
  let id_librero = 0
  let posicion = 0
  let selectedVolumen = null

  onMount(async () => {
    await getVolumenes()
  })

  const setVolumenes = (data) => {
    volumenes = data
  }

  const clearFields = () => {
    titulo = ''
    id_estante = 0
    descripcion_estante = ''
    ubicacion_estante = ''
    id_sala = 0
    id_librero = 0
    posicion = 0
    selectedVolumen = null
  }

  const getVolumenes = async () => {
    const response = await axios.get(API_URL + '/volumenes')
    setVolumenes(response.data)
  }

  const addVolumen = async () => {
    const estante = {
      idEstante: idestante,
      descripcion: descripcion_estante,
      ubicacion: ubicacion_estante
    }

    const data = {
      titulo,
      estante,
      idSala: id_sala,
      idLibrero: id_librero,
      posicion: posicion
    }

    await axios.post(API_URL + '/volumenes', data)
    await getVolumenes()
    clearFields()
  }

  const deleteVolumen = async (id) => {
    await axios.delete(`${API_URL}/volumenes/${id}`)
    await getVolumenes()
  }

  const editVolumen = async () => {
    const estante = {
      idEstante: id_estante,
      descripcion: descripcion_estante,
      ubicacion: ubicacion_estante
    }

    const data = {
      titulo,
      estante,
      idSala: id_sala,
      idLibrero: id_librero,
      posicion: posicion
    }

    await axios.put(`${API_URL}/volumenes/${selectedVolumen.id_volumen}`, data)
    await getVolumenes()
    clearFields()
  }

  const getVolumenById = async (id) => {
    const response = await axios.get(`${API_URL}/volumenes/${id}`)
    const volumen = response.data

    titulo = volumen.titulo
    id_estante = volumen.estante.idEstante
    descripcion_estante = volumen.estante.descripcion
    ubicacion_estante = volumen.estante.ubicacion
    id_sala = volumen.idSala
    id_librero = volumen.idLibrero
    posicion = volumen.posicion
    selectedVolumen = volumen
  }
</script>

<body style="background-color: var(--color-primary);">
<div>
  <h1>Volumenes</h1>

<form on:submit|preventDefault={selectedVolumen === null ? addVolumen : editVolumen}>
  <table>
    <tr>
      <th>Título</th>
      <td><input id="titulo" type="text" bind:value={titulo}/></td>
    </tr>
    <tr>
      <th>Estante ID</th>
      <td><input id="estante" type="number" bind:value={id_estante}/></td>
    </tr>
    <tr>
      <th>Descripción estante</th>
      <td><input id="descripcion_estante" type="text" bind:value={descripcion_estante}/></td>
    </tr>
    <tr>
      <th>Ubicación estante</th>
      <td><input id="ubicacion_estante" type="text" bind:value={ubicacion_estante}/></td>
    </tr>
    <tr>
      <th>Sala</th>
      <td><input id="sala" type="number" bind:value={id_sala}/></td>
    </tr>
    <tr>
      <th>Librero</th>
      <td><input id="librero" type="number" bind:value={id_librero}/></td>
    </tr>
    <tr>
      <th>Posición</th>
      <td><input id="posicion" type="number" bind:value={posicion}/></td>
    </tr>
    <tr>
      <td colspan="2">
        {#if selectedVolumen === null}
          <button type="submit">Agregar</button>
        {:else}
          <button type="submit">Editar</button>
        {/if}
        <button type="button" on:click={clearFields}>Cancelar</button>
      </td>
    </tr>
  </table>
</form>

  <table>
    <thead>
      <tr>
        <th>Título</th>
        <th>Estante ID</th>
        <th>Descripción </th>
        <th>Ubicación</th>
        <th>Sala</th>
        <th>Librero</th>
        <th>Posición</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      {#each volumenes as volumen}
      <tr>
        <td>{volumen.titulo}</td>
        <td>{volumen.estante.idEstante}</td>
        <td>{volumen.estante.descripcion}</td>
        <td>{volumen.estante.ubicacion}</td>
        <td>{volumen.idSala}</td>
        <td>{volumen.idLibrero}</td>
        <td>{volumen.posicion}</td>
        <td>
          <button on:click={() => getVolumenById(volumen.id_volumen)}>Editar</button>
          <button on:click={() => deleteVolumen(volumen.id_volumen)}>Eliminar</button>
        </td>
      </tr>
      {/each}
    </tbody>
  </table>
</div>

<div>
<h1>Estantes</h1>
<table>
  <thead>
    <tr>
      <th>Descripción</th>
      <th>Ubicación</th>
      <th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {#each estantes as estante}
      <tr>
        <td>{estante.descripcion}</td>
        <td>{estante.ubicacion}</td>
        <td>
          <button on:click={() => deleteEstante(estante.id_estante)}>Eliminar</button>
          <button on:click={() => getEstanteById(estante.id_estante)}>Editar</button>
        </td>
      </tr>
    {/each}
  </tbody>
</table>

  <form on:submit|preventDefault={id_estante === null ? addEstante : editEstante}>
          <table>
              <tr>
                  <th>Descripción:</th>
                  <td><input type="text" bind:value={descripcion} /></td>
              </tr>
              <tr>
                  <th>Ubicación:</th>
                  <td><input type="text" bind:value={ubicacion} /></td>
              </tr>
              {#if id_estante !== null}
                  <tr>
                      <td colspan="2">
                          <button type="submit">Editar Estante</button>
                          <button type="button" on:click={clearForm}>Cancelar</button>
                      </td>
                  </tr>
              {:else}
                  <tr>
                      <td colspan="2">
                          <button type="submit">Agregar Estante</button>
                      </td>
                  </tr>
              {/if}
          </table>
      </form>
</div>
</body>

<style>
   table {
     background-color: #F5F5F5;
     width: 100%;
     border-collapse: collapse;
   }

   th,
   td {
     border: solid 1px #ccc;
     padding: 12px;
     text-align: left;
   }

   th {
     background-color: #EEE;
   }
    /* Style for the Volumenes Table */
    table {
      border-collapse: collapse;
      width: 100%;
    }

    th, td {
      padding: 8px;
      text-align: left;
      border-bottom: 1px solid #ddd;
    }

    th {
      background-color: #f2f2f2;
    }

    tbody tr:hover {
      background-color: #f5f5f5;
    }

    /* Style for the Estantes List */
    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    li {
      margin-bottom: 1em;
    }

    button {
      margin-left: 1em;
      padding: 0.5em 1em;
      border-radius: 4px;
      background-color: #eaeaea;
      border: none;
      font-size: 1em;
      cursor: pointer;
    }

    button:hover {
      background-color: #ddd;
    }

    /* Additional styles */
    :root {
      --color-primary: #8342f0; /* Primary color for 2023 */
      --color-secondary: #f08c42; /* Secondary color for 2023 */
    }

    button[type="submit"] {
      background-color: var(--color-primary);
      color: white;
    }

    button[type="submit"]:hover {
      background-color: var(--color-secondary);
    }
  </style>