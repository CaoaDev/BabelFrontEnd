<script>
  import { onMount } from 'svelte'
  import axios from 'axios'

  const API_URL = 'http://localhost:8080/volumenes'

  let volumenes = []
  let titulo = ''
  let id_estante = 0
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
    const response = await axios.get(API_URL)
    setVolumenes(response.data)
  }

const addVolumen = async () => {
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

  await axios.post(API_URL, JSON.stringify(data), {
    headers: {
      'Content-Type': 'application/json'
    }
  })

  await getVolumenes()
  clearFields()
}

const deleteVolumen = async (id) => {
  await axios.delete(`${API_URL}/${id}`);
  await getVolumenes();
};

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

    await axios.put(`${API_URL}/${selectedVolumen.id_volumen}`, data)
    await getVolumenes()
    clearFields()
  }

  const getVolumenById = async (id) => {
    const response = await axios.get(`${API_URL}/${id}`)
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

<div>
  <h1>Volumenes</h1>

  <form on:submit|preventDefault={selectedVolumen === null ? addVolumen : editVolumen}>
    <div>
      <label for="titulo">Título:</label>
      <input id="titulo" type="text" bind:value={titulo}/>
    </div>
    <div>
      <label for="estante">Estante ID:</label>
      <input id="estante" type="number" bind:value={id_estante}/>
    </div>
    <div>
      <label for="descripcion_estante">Descripción estante:</label>
      <input id="descripcion_estante" type="text" bind:value={descripcion_estante}/>
    </div>
    <div>
      <label for="ubicacion_estante">Ubicación estante:</label>
      <input id="ubicacion_estante" type="text" bind:value={ubicacion_estante}/>
    </div>
    <div>
      <label for="sala">Sala:</label>
      <input id="sala" type="number" bind:value={id_sala}/>
    </div>
    <div>
      <label for="librero">Librero:</label>
      <input id="librero" type="number" bind:value={id_librero}/>
    </div>
    <div>
      <label for="posicion">Posición:</label>
      <input id="posicion" type="number" bind:value={posicion}/>
    </div>
    {#if selectedVolumen === null}
      <button type="submit">Agregar</button>
    {:else}
      <button type="submit">Editar</button>
    {/if}
    <button type="button" on:click={clearFields}>Cancelar</button>
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
        <button on:click={() => { selectedVolumen = volumen; getVolumenById(volumen.id_volumen) }}>Editar</button>
        <button on:click={() => deleteVolumen(volumen.idvolumen)}>Eliminar</button>
        </td>
      </tr>
      {/each}
    </tbody>
  </table>
</div>