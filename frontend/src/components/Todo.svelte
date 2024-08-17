<script>
    export let task;
    export let toggleComplete;
    export let deleteTodo;
    export let updateTodo;
    export let isCompleted = false;

    const formatDate = (dateTimeString) => {
        const date = new Date(dateTimeString);

        return date.toLocaleDateString(undefined, {
            year: 'numeric',
            month: 'long',
            day: 'numeric',
        });
    };

    const formatTime = (dateTimeString) => {
        const date = new Date(dateTimeString);

        return date.toLocaleTimeString(undefined, {
            hour: 'numeric',
            minute: 'numeric',
            hour12: true,
        });
    };
</script>

<div class={`Todo ${isCompleted ? 'completed-task' : ''} priority-${task.priority.toLowerCase()}`}>
    <p
            on:click={() => toggleComplete(task.id)}
       class={task.status ? 'completed' : 'incompleted'}>
        {task.title}
    </p>
    <div>
        <span>{formatDate(task.datetime)}</span>
        <span>{formatTime(task.datetime)}</span>
        <img
                class="fa-pen"
                src="https://static-00.iconduck.com/assets.00/edit-icon-1022x1024-kes437mc.png"
                alt="Pen Icon"
                on:click={() => updateTodo(task.id)}
        />
        <img
                class="fa-trash"
                src="https://img.icons8.com/ios_filled/200/FFFFFF/trash.png"
                alt="Trash Icon"
                on:click={() => deleteTodo(task.id)}
        />
    </div>
</div>

<style>
</style>
