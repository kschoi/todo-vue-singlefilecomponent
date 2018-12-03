<template>
    <header class="header">
        <h1>todos</h1>
        <input class="new-todo"
            autofocus autocomplete="off"
            placeholder="할 일을 적어주세요!"
            v-model="newTodo"
            @keyup.enter="addTodo">
    </header>
</template>

<script>
export default {
    data () {
        return {
            newTodo: '',
        }
    },

    methods: {
        addTodo () {
            var value = this.newTodo && this.newTodo.trim()
            if (!value) {
                return
            }
            this.$emit('add:todo', value);
            this.clearInput();
        },
        clearInput () {
            this.newTodo = '';
        }
    },

}
</script>

<style scoped>
h1 {
	position: absolute;
	top: -155px;
	width: 100%;
	font-size: 100px;
	font-weight: 100;
	text-align: center;
	color: rgba(175, 47, 47, 0.15);
	-webkit-text-rendering: optimizeLegibility;
	-moz-text-rendering: optimizeLegibility;
	text-rendering: optimizeLegibility;
}

.new-todo{
	position: relative;
	margin: 0;
	width: 100%;
	font-size: 24px;
	font-family: inherit;
	font-weight: inherit;
	line-height: 1.4em;
	border: 0;
	color: inherit;
	padding: 16px 16px 16px 60px;
	border: 0;
	background: rgba(0, 0, 0, 0.003);
	box-shadow: inset 0 -2px 1px rgba(0,0,0,0.03);
	box-sizing: border-box;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}
</style>
