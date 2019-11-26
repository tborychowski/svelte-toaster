
<Toaster position="bottom" />

<button on:click="{() => showToast('Hello')}">Show info</button>
<button on:click="{() => showToast('Hello', 'warning')}">Show warning</button>
<button on:click="{() => showToast('Hello', 'error', 100000, 'Undo', cb)}">Show error</button>
<button on:click="{() => showToast('Hello', 'error', 10000, 'Undo', cb)}">Show error</button>


<script>

import Toaster, { showToast } from './Toaster';

function cb (e, id) {
	if (e.target.closest('button')) console.log(id);

}

</script>
