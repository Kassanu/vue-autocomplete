<template>
    <div class="input-group">
        <input 
            class="form-control"
            type="text" 
            :placeholder="placeholder"
            :name="name"
            :value="value"
            @input="inputChange"
            @blur="blur" 
            @focus="focus"
            @keyup.enter.prevent="keyEnter" 
            @keydown.up.prevent="keyUp" 
            @keydown.down.prevent="keyDown"
        > 
        <div class="dropdown-menu dropdown-menu-scrollable w-100" v-bind:class="{ show: show }">
            <div :is="itemComponent" v-for="(item, i) in filtered" :item="item" :key="item.id" @click="onClickItem(item)" :class="{'active': i === cursor}" @mouseover="cursor = i"></div>
        </div>
    </div>
</template>

<script>
    import VueAutocompleteItem from './VueAutocompleteItem';
    import axios from 'axios'

    export default {
        name: 'VueAutocomplete',
        components: {
            VueAutocompleteItem
        },
        props: {
            startValue: {
                type: String,
                default: ""
            },
            items: {
                type: Array,
                default: () => []
            },
            remote: {
                type: Boolean,
                default: false
            },
            remoteUrl: {
                type: String,
                default: ''
            },
            filterKey: {
                type: String,
                required: true
            },
            minLength: {
                type: Number,
                default: 3
            },
            returnAmount: {
                type: Number,
                default: 0
            },
            placeholder: {
                type: String,
                default: ''
            },
            name: {
                type: String,
                default: ''
            },
            getLabel: {
                type: Function,
                default: item => item
            },
            itemComponent: { 
                type: String, 
                default: 'VueAutocompleteItem'
            }
        },
        data: function() {
            return {
                value: "",
                listOpen: false,
                keepOpen: false,
                remoteItems: [],
                cursor: 0
            }
        },
        methods: {
            fetchItems: _.debounce(function (key, value) {
                if ( this.remote && typeof this.remoteUrl === 'string' && this.remoteUrl.length > 0) {
                    axios.get(this.remoteUrl, {params: {column: key, query: value, limit: this.returnAmount}})
                    .then(response =>{
                        this.remoteItems = response.data;
                    })
                    .catch(error => {
                        console.log(error);
                    });
                }
                else {
                    this.remoteItems = [];
                }
            }, 500, {leading: false, trailing: true}),
            reset() {
                this.value = '';
            },
            hideList() {
                this.listOpen = false;
            },
            showList() {
                this.listOpen = true;
            },
            inputChange (event) {
                this.value = event.target.value;
                this.cursor = 0;
                if (this.remote && this.value.length > this.minLength) {
                    this.fetchItems(this.filterKey, this.value);
                }
                else if (this.remote && this.value.length < this.minLength) {
                    this.remoteItems = [];
                }

                if (this.filtered.length > 0) {
                    if (this.value.toLowerCase() === this.filtered[0][this.filterKey].toLowerCase()) {
                        this.value = this.filtered[0][this.filterKey];
                    }
                    this.showList();
                }

                this.$emit('input', this.value);
                this.$emit('update');
            },
            focus () {
                this.$emit('focus', this.value);
                this.showList();
                this.cursor = 0;
            },
            blur () {
                this.$emit('blur', this.value);
                setTimeout( () => this.hideList(), 200);
            },
            onClickItem(item) {
                this.onSelectItem(item);
                this.$emit('item-clicked', item);
            },
            onSelectItem (item) {
                if (this.remote) {
                    this.remoteItems = [];
                }
                this.value = this.getLabel(item);
                this.$emit('input', this.getLabel(item));
                this.$emit('update');
            },
            keyUp (e) {
                if (this.cursor > 0) {
                    this.cursor--;
                    let item = this.$el.getElementsByClassName('dropdown-item')[this.cursor];
                    item.scrollIntoView(false);
                }
            },
            keyDown (e) {
                let len = this.remoteItems.length || this.filtered.length;
                if (this.cursor < len - 1) {
                    this.cursor++;
                    let item = this.$el.getElementsByClassName('dropdown-item')[this.cursor];
                    item.scrollIntoView(false);
                }
            },
            keyEnter (e) {
                if (this.remote && this.remoteItems.length > 0) {
                    this.onSelectItem(this.remoteItems[this.cursor]);
                    this.hideList();
                }
                else if(!this.remote && this.filtered.length > 0) {
                    this.onSelectItem(this.filtered[this.cursor]);
                    this.hideList();
                }
            },
        },
        computed: {
            filtered() {

                if (this.remote === true) {
                    return this.remoteItems;
                }

                if(this.value !== undefined && this.value.length >= this.minLength) {
                    let filt = this.items.filter(item => {
                        if( item.hasOwnProperty(this.filterKey)  ) {
                            return item[this.filterKey]
                                    .toLowerCase()
                                    .indexOf(this.value.toLowerCase()) > -1;
                        } else {
                            console.error(`Seems like property you passed down ${this.filterKey} doesn't exist on object ! `);
                        }
                    });
                    
                    if (this.value.length > 0) {
                        filt.sort((a, b) => {
                            return a[this.filterKey].toLowerCase().indexOf(this.value.toLowerCase()) < b[this.filterKey].toLowerCase().indexOf(this.value.toLowerCase());
                        });
                    }

                    if (this.returnAmount > 0) {
                        return filt.slice(0, this.returnAmount);
                    }
                    else {
                        return filt;
                    }
                }

                return [];
            },
            isEmpty() {
                if( typeof this.filtered === 'undefined'  ) {
                    return false;
                } else {
                    return this.filtered.length < 1;
                }
            },
            hasItems () {
                if( typeof this.filtered === 'undefined'  ) {
                    return false;
                } else {
                    return this.remove?!!this.remoteItems.length:!!this.filtered.length;
                }
            },
            show () {
                return (this.listOpen && this.hasItems) || this.keepOpen
            }
        },
        mounted() {
            this.value = this.startValue;
        }
    }
</script>


<style scoped>

</style>
  