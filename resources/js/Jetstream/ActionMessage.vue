<template>
    <div>
        <transition
            leave-active-class="transition ease-in duration-1000"
            leave-class="opacity-100"
            leave-to-class="opacity-0"
        >
            <div v-show="on" class="text-muted pt-2">
                <slot />
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    props: ["on"],
};
</script>
