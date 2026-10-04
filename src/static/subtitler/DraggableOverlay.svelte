<style>
	.overlay {
		position: fixed;
		inset: 0;
		pointer-events: none;
	}
	.drag-handle {
		position: absolute;
		top: 1rem;
		left: 50%;
		transform: translateX(-50%);
		z-index: 2147483647;
		pointer-events: auto;
		cursor: move;
		padding: 0.35rem 0.65rem;
		border: 1px solid #fff;
		border-radius: 0.3rem;
		background: rgba(0, 0, 0, 0.8);
		color: white;
		font: 14px sans-serif;
		touch-action: none;
	}
</style>

<div class="overlay" style="transform: translate({$subtitleOffset.x}px, {$subtitleOffset.y}px)">
	<slot />
	{#if $subtitleDragEnabled}
		<button
			class="drag-handle"
			title="Drag subtitle position"
			aria-label="Drag subtitle position"
			on:pointerdown={startDrag}
			on:pointermove={moveDrag}
			on:pointerup={endDrag}
			on:pointercancel={endDrag}>↕ Drag Jimaku</button
		>
	{/if}
</div>

<script lang="ts">
	import { subtitleDragEnabled, subtitleOffset } from './stores/settings';
	import { get } from 'svelte/store';

	let dragStart = { x: 0, y: 0, offsetX: 0, offsetY: 0 };

	function startDrag(event: PointerEvent) {
		// eslint-disable-next-line no-undef
		event.preventDefault();
		(event.currentTarget as HTMLElement).setPointerCapture(event.pointerId);
		const offset = get(subtitleOffset);
		dragStart = { x: event.clientX, y: event.clientY, offsetX: offset.x, offsetY: offset.y };
	}

	function moveDrag(event: PointerEvent) {
		if (!event.buttons) return;
		subtitleOffset.set({
			x: dragStart.offsetX + event.clientX - dragStart.x,
			y: dragStart.offsetY + event.clientY - dragStart.y,
		});
	}

	function endDrag(event: PointerEvent) {
		const target = event.currentTarget as HTMLElement;
		if (target.hasPointerCapture(event.pointerId)) target.releasePointerCapture(event.pointerId);
	}
</script>
