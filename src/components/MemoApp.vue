<template>
    <div class="memo-app">
        <memo-form @addMemo="addMemo" />

        <ul class="memo-list">
            <memo v-for="memo in memos" :key="memo.id" :memo="memo"
                  @deleteMemo="deleteMemo"
                  @updateMemo="updateMemo"
                    :editingId="editingId"
                  @setEditingId="SET_EDITING_ID"
                  @resetEditingId="RESET_EDITING_ID"
            />
        </ul>
    </div>
</template>
<script>
import MemoForm from './MemoForm';
import Memo from './Memo';
import axios from 'axios';
import {mapActions,mapState, mapMutations} from 'vuex';
import {RESET_EDITING_ID,SET_EDITING_ID} from '../store/mutations-type'

const memoAPICore=axios.create({
    baseURL:'http://localhost:8000/api/memos'
});

export default {
    name:'MemoApp',
    //data(){
     //   return{
     //       memos:[],
     //   };
    //},
    created(){
        //this.memos=localStorage.memos ? JSON.parse(localStorage.memos) : [];
        //memoAPICore.get('/')
          //  .then(res=>{
            //    this.memos=res.data;
            //});
        this.fetchMemos();
    },
    components:{
        MemoForm,
        Memo
    },
    methods:{
        addMemo(payload){
            //this.memos.push(payload);
            //this.storeMemo();
            memoAPICore.post('/',payload)
                .then(res=>{
                    this.memos.push(res.data);
                });
        },
        storeMemo(){
            const memosToString=JSON.stringify(this.memos);
            localStorage.setItem('memos',memosToString);
        },
        
        updateMemo(payload){
            const {id,content}=payload;
            const targetIndex=this.memos.findIndex(v=>v.id===id);
            const targetMemo=this.memos[targetIndex];
            //this.memos.splice(targetIndex,1,{...targetMemo,content});
            //this.storeMemo();
            memoAPICore.put(`/${id}`,{content})
                .then(()=>{
                    this.memos.splice(targetIndex,1,{...targetMemo,content});
                });

        },

        ...mapActions([
            'fetchMemos',
            'addMemo',
            'deleteMemo',
            'updateMemo'
        ]),

        ...mapMutations([
            SET_EDITING_ID,
            RESET_EDITING_ID,
        ]),
    
    },
    computed:{
        ...mapState([
            'memos',
            'editingId'
        ])
    },
    
}
</script>
<style scoped>
    .memo-list{
        padding:20px 0;
        margin:0;
    }
</style>