<script>
    export let updateTodo;
    export let task;

    let title = '';
    let dateTime = '';
    let priority = '';

    $: if (task) {
        title = task.title || '';
        dateTime = task.datetime
            ? new Date(task.datetime).toLocaleString('sv-SE').replace(' ', 'T')
            : '';
        priority = task.priority || '';
    }

    function handleDateChange(e) {
        const newDate = e.target.value;
        const time = dateTime.split('T')[1] || '00:00:00';
        dateTime = `${newDate}T${time}`;
    }

    function handleTimeChange(e) {
        const newTime = `${e.target.value}:00`;
        const date = dateTime.split('T')[0] || '';
        dateTime = `${date}T${newTime}`;
    }

    function handlePriorityChange(e) {
        priority = e.target.value;
    }

    function handleTitleChange(e) {
        title = e.target.value;
    }

    async function handleSubmit(e) {
        e.preventDefault();

        const isoDateTime = new Date(dateTime).toISOString();
        updateTodo(task.id, title, isoDateTime, priority);
    }

</script>

<form class='todo-form' on:submit={handleSubmit}>
    <input
            type='text'
            class='todo-input'
            value={title}
            on:change={handleTitleChange}
            placeholder='Update Task'
    />
    <input
            type='date'
            class='todo-input-data'
            value={dateTime.split('T')[0]}
            on:change={handleDateChange}
    />
    <input
            type='time'
            class='todo-input-time'
            value={dateTime.split('T')[1]?.substring(0, 5) || ''}
            on:change={handleTimeChange}
    />
    <select
            class="todo-input-priority"
            value={priority}
            on:change={handlePriorityChange}>
        <option value="">Select Priority</option>
        <option value="High">High</option>
        <option value="Medium">Medium</option>
        <option value="Low">Low</option>
    </select>
    <button type='submit' class='todo-btn'>
        Update Task
    </button>
</form>

<style>
</style>