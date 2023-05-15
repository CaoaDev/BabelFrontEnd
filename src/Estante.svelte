<script>
  import axios from 'axios';

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
    await axios.put(`${API_URL}/estantes/${id_estante}`, data);
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
</script>

<div>
 <h1>Estantes</h1>
  <ul>
    {#each estantes as estante}
      <li>
        {estante.descripcion} ({estante.ubicacion})
        <button on:click={() => deleteEstante(estante.id_estante)}>Delete</button>
        <button on:click={() => getEstanteById(estante.id_estante)}>Edit</button>
      </li>
    {/each}
  </ul>

  <form on:submit|preventDefault={id_estante === null ? addEstante : editEstante}>
    <label>
      Descripción:
      <input type="text" bind:value={descripcion} />
    </label>
    <label>
      Ubicación:
      <input type="text" bind:value={ubicacion} />
    </label>
    {#if id_estante !== null}
      <button type="submit">Editar Estante</button>
      <button type="button" on:click={clearForm}>Cancelar</button>
    {:else}
      <button type="submit">Agregar Estante</button>
    {/if}
  </form>
</div>

