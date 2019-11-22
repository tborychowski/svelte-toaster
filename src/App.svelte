
<Toaster />

<button on:click="{() => showToast('Hello')}">Show info</button>
<button on:click="{() => showToast('Hello', 'warning')}">Show warning</button>
<button on:click="{() => showToast('Hello', 'error', false)}">Show error</button>


<script>

import Toaster, { showToast } from './Toaster';


</script>
