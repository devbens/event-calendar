<template>
    <button :type="type" class="btn btn-outline-secondary">
        <slot></slot>
    </button>
</template>

<script>
export default {
    props: {
        type: {
            type: String,
            default: "button",
        },
    },
};
</script>
