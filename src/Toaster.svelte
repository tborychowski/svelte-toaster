<style>
.toaster {
	position: fixed;
	width: 400px;
	left: 50%;
	transform: translateX(-50%);
	padding-top: 5px;
	display: flex;
	flex-flow: column;
	align-items: stretch;
	user-select: none;
	z-index: 90;
}
.toaster-top { top: 0; }
.toaster-bottom { bottom: 0; }
.toast {
	position: relative;
	overflow: hidden;
	flex: 1;
	margin-top: 5px;
	margin-bottom: 5px;
	text-align: center;
	box-shadow: 0 10px 20px 2px rgba(0,0,0,0.2);
	z-index: 10;
	font-size: 15px;
	display: flex;
	align-items: center;
	justify-content: space-between;
	min-height: 30px;
	border-radius: 25px;
}
.toast button {
	background: none;
	border: none;
	color: #eee;
	height: 30px;
	min-width: 38px;
	padding: 0 10px;
	display: flex;
	align-items: center;
	justify-content: center;
	transition: background-color .2s;
}
.toast button:hover { color: #fff; background: #3338; }
.toast button:active { color: #bbb; background: #0008; }

.toast .toast-close {
	font-size: 22px;
	font-weight: 200;
	border-radius: 0 25px 25px 0;
	position: relative;
	padding-bottom: 3px;
}

.toast,
.toast-info { background: #666d7b; }
.toast-warning { background: #af8a1a; }
.toast-error { background: #8b4848; }
.toast-msg { color: #eee; flex: 1; }
.toast-progressbar {
	position: absolute;
	bottom: 1px;
	left: 1px;
	right: 1px;
	top: 1px;
	border-radius: 25px;
	overflow: hidden;
	z-index: -1;
}
.toast-progress {
	height: 100%;
	border-radius: 25px;
	background-color: #fff1;
	width: 0;
	transition: width 0.2s;
}
</style>

<div class="toaster toaster-{position}">
	{#each toasts as toast (toast.id)}
		<div class="toast toast-{toast.type}"
			transition:scale="{{ start: 0.5 }}"
			on:click|preventDefault="{e => toast.cb(e, toast.id)}">
				<div class="toast-msg">{@html toast.msg}</div>
				{#if toast.btn}
					<button>{toast.btn}</button>
				{/if}
				<button class="toast-close" on:click|stopPropagation="{() => hideToast(toast.id)}">&times;</button>
				{#if toast.showProgress}
					<div class="toast-progressbar">
						<div class="toast-progress" style="width: {progress[toast.id]}%"></div>
					</div>
				{/if}
			</div>
	{/each}
</div>


<script context="module">
import { writable } from 'svelte/store';
import { scale } from 'svelte/transition';

const _toasts = writable({});

export function showToast (msg, type = 'info', timeout = 5000, btn, cb = () => {}) {
	const id = guid();
	let showProgress = false;
	if (typeof timeout === 'number') {
		setTimeout(() => hideToast(id), timeout);
		showProgress = true;
		timeout = timeout - 500;
	}
	_toasts.update(list => {
		list[id] = { type, msg, id, timeout, cb, showProgress, btn };
		return list;
	});
	return id;
}

export function hideToast (id) {
	_toasts.update(list => {
		delete list[id];
		return list;
	});
}

function guid () {
	return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, c => {
		const r = Math.random() * 16 | 0;
		const v = c == 'x' ? r : (r & 0x3 | 0x8);
		return v.toString(16);
	});
}
</script>

<script>
export let position = 'top';
let toasts = [], timers = {}, progress = {};

_toasts.subscribe(val => {
	toasts = Object.values(val);
	toasts.forEach(t => {
		if (!timers[t.id]) createTimer(t.id, t.timeout);
	});
});

function createTimer (id, timeout) {
	progress[id] = 0;
	timers[id] = setInterval(() => {
		progress[id] += 1;
		if (progress[id] >= 100) clearInterval(timers[id]);
	}, timeout / 100);
}
</script>
