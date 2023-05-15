<script>
//import Volumen from './Components/Volumen.svelte'

import axios from 'axios'
let titulo = ''
let autor = ''
let editorial = ''

let resultados = []

const conectar = async () => {
  const res = await axios.get('http://localhost:8080/volumenes')
  resultados = Object.values(res.data)
}

conectar()

const guardar = async () => {
  const data = { titulo, autor, editorial }
  const res = await axios.post('/volumenes', data)
  resultados = res.data
}
</script>

<!-- <Volumen /> -->

<h2>Agregar nuevo volumen</h2>
<form on:submit|preventDefault={guardar}>
  <label>
    Título:
    <input type="text" bind:value={titulo} />
  </label>
  <label>
    Autor:
    <input type="text" bind:value={autor} />
  </label>
  <label>
    Editorial:
    <input type="text" bind:value={editorial} />
  </label>
  <button type="submit">Crear</button>
</form>

<table>
  <thead>
    <tr>
      <th>Título</th>
      <th>Autor</th>
      <th>Editorial</th>
      <th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {#each resultados as volumen}
      <tr>
        <td>{volumen.titulo}</td>
        <td>{volumen.autor}</td>
        <td>{volumen.editorial}</td>
        <td>
          <button on:click={() => editar(volumen)}>Editar</button>
          <button on:click={() => eliminar(volumen)}>Eliminar</button>
        </td>
      </tr>
    {/each}
  </tbody>
</table>