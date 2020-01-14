<template>
    <div>
        <b-nav tabs>
            <b-nav-item disabled>Quiz App</b-nav-item>
            <b-nav-item disabled>Counter: {{ correctTotal + ' / ' + answeredTotal }}</b-nav-item>
        </b-nav>
    </div>
</template>

<script>
export default {
    props: {
        correctTotal: Number,
        answeredTotal: Number,
    }
}
</script>