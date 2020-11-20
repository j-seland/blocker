<script>
import { onMount } from "svelte";
import Board from './components/Board.svelte';

onMount(() => {
  document.title = 'Blocker v2.0.1';
})
</script>

<Board />
