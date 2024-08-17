<script>
    export let message;
    export let onClose;
</script>

<div class="modal">
    <div class="modal-content">
        <p>{message}</p>
        <button class="close-btn" on:click={onClose}>Close</button>
    </div>
</div>

<style>
</style>
