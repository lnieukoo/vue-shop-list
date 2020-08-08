Vue.component('shop-list', {
    template:`
        <div id='shop-list'>
            <section class="hero">
            <div class="hero-body">
            <div class="container has-text-centered">
                <h1 class='title'>Shopping List</h1>
                <shop-items></shop-items>
            </div>
            </div>
            </section>
        </div>
    `
})

Vue.component('shop-items' , {
    data: function() {
        return {
            items: [],
            newitem: null,
            show: false,
            checked_items_count: 0
        }
    },
    computed: {
        itemCount(){
            return this.items.length
        },
    },
    mounted() {
            localStorage.show ? this.show = JSON.parse(localStorage.show) : null
            localStorage.checked_items_count ? this.checked_items_count = JSON.parse(localStorage.checked_items_count) : null
            this.items = JSON.parse(localStorage.getItem("items") || "[]");

            for(i in this.items){
                //this.items[i].show = JSON.parse(this.items[i].show)
                console.log(typeof(this.items[i].show))
            }

          },
    methods: {
        deleteItem(index){
            this.items[index].complete ? this.checked_items_count -= 1 : null
            this.items.splice(index, 1)
        },
        checkItem(index){
            if (this.items[index].complete){
                this.items[index].complete = false
                this.items[index].show = true
                this.moveItemFront(index)
                this.checked_items_count -= 1
            }
            else{
                this.items[index].complete = true
                this.show ? this.items[index].show = true : this.items[index].show = false
                this.moveItemEnd(index)
                this.checked_items_count += 1
            } 
        },
        addItem(){
            if(this.newitem != null){
            this.items.push(
                
                {
                    name:this.newitem, 
                    complete: false,
                    show: true
                })
            this.newitem = null}
        },
        moveItemEnd(index){
            let move_item = this.items[index]
            this.items.splice(index, 1)
            this.items.push(move_item)
        },
        moveItemFront(index){
            this.items.unshift(this.items.splice(index, 1)[0]);

        },
        deleteAll(){
            this.items = []
            this.checked_items_count = 0
        }

    },
    watch: {

        show(newShow){
                
            for (item in this.items){
                this.items[item].complete ? this.items[item].show = this.show : null
            }

            localStorage.show = newShow
            localStorage.setItem("items", JSON.stringify(this.items));
        },
        checked_items_count(newCount){
            localStorage.checked_items_count = newCount
        },
        items(newItems){
            localStorage.setItem("items", JSON.stringify(newItems));
        }
    },
    
    template:`
    
    <div id="todo-item">
        <div v-if="items.length >= 1" >
            <table class="table table-responsive is-striped is-fullwidth">
                <div v-for="(item, index) in items">
                    <span v-show="item.show">
                    <tr>
                    <td class="td-todo-60 v-centered">
                        <p class="has-text-left"><small><em>item {{index+1}}</em></small></p>
                        <span class="is-capitalized" v-if="item.complete"><p><del>{{item.name}}</del></p></span>
                        <span class="is-capitalized" v-else><p>{{item.name}}</p></span>
                    </td>
                    <td class="td-todo-20">
                        <button @click="deleteItem(index)" class="button is-danger is-small"> X </button>
                    </td>   
                    <td class="td-todo-20">
                        <span v-if="item.complete">
                            <button @click="checkItem(index)" class="button is-warning is-small"> Uncheck </button>
                        </span>
                        <span v-else>
                            <button @click="checkItem(index)" class="button is-success is-small"> Check </button>
                        </span>        
                    </td>
                </tr>
            </span>
            </div>
            </table>
            <p><small>{{checked_items_count}}/{{itemCount}} items checked</small></p>
        </div>
        <div v-else>
         <p>Please add an item to your shopping list</p>
        </div>
        <br>
        <p class="potential-item" v-if="newitem">{{newitem}}</p>
        <br>
        <label class="checkbox">
            <input type="checkbox" v-model="show">
            Show checked items
        </label>
        <br>
        <input @keyup.enter="addItem()" class="input" v-model="newitem">
        <br>
        <br>    
        <button @click="addItem()" class="button is-success"> Add </button>
        <button @click="deleteAll()" class="button is-danger"> Delete All </button>
    </div>

    `

})

const app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})

