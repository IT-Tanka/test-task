<template>
  <ag-grid-vue
    style="width: 100%; height: 100VH; margin: 20px auto;"
    class="ag-theme-alpine"
    :columnDefs="columnDefs.value"
    :rowData="rowData.value"
    @click="showInfo($event)"
  >
  </ag-grid-vue>
</template>

<script>
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { AgGridVue } from "ag-grid-vue3";
import {reactive, h} from "vue";
const ImgComp={
  setup(props){
    const{params}=props;
    return() => h("img", {
    src: params.value, style:"height:30px"});
  },
};
export default {
  name: "App",
  components: {
    AgGridVue,
    ImgComp
  },
  data(){
    return{ 
      dataArray:[], 
      n:100
    }
  },
  setup() {
    const columnDefs=reactive({
      value: [
        {headerName: 'GROUP1',
        children: [
          { field: "Col1", filter:false, cellRenderer:'ImgComp' },
          { field: "Col2", filter:false },
          { field: "Col3", filter:false },
        ]},
        {headerName: 'GROUP2',
        children: [
          { field: "Col4", filter:false},
          { field: "Col5", filter:false,  cellStyle: {'text-decoration': 'underline', 'font-weight': 'bold'}},
          { field: "Col6", filter: 'agTextColumnFilter'},
        ]
        },
      ],
    }); 
    const rowData=reactive({
      value:[],
    });
    return {
      rowData,
      columnDefs,
    };
  },
  methods:{
    createRandomInt(floatNum=0, min=0, max=0){
      if(!(min && max)){
        return (Math.random()*1000).toFixed(floatNum)
      }
      else{ 
        return (Math.random() * (max - min) + min).toFixed(floatNum);
      }  
    },
    createRandomString(stringLength) { 
      let str = ''; 
      while (!str) str = Math.random().toString(36).substring(2,stringLength+2);
      return str; 
    },
    createAndRenderData(){
      let sumCol3=0;
      let middleCol4=0;
      let str1='first string',str2='second string', str3='third string', str4='fourth string',str5='fifth string';
      for (let i=0; i < this.n ; i++){
        let elem={};
        elem.val1=this.createRandomString(10);
        elem.val2=this.createRandomInt(2);
        sumCol3+=Number(elem.val2);
        elem.val3=this.createRandomInt(4);
        elem.val4=this.createRandomInt(0);
        elem.val5=this.createRandomInt(0);
        middleCol4+=Number(elem.val4)+Number(elem.val5);   
        elem.val6='src/img/image-'+this.createRandomInt(0,1,5)+'.png';
        elem.val7=this.createRandomString(10);
        elem.val8=[str1, str2, str3, str4, str5][this.createRandomInt(0,1,5)-1];
        this.dataArray.push(elem);
        this.rowData.value[i]={
          Col1: elem.val6, 
          Col2: elem.val1, 
          Col3: elem.val2+"kg", 
          Col4:Number(elem.val4)+ Number(elem.val5), 
          Col5:elem.val7,
          Col6:elem.val8 
        };
    }
      this.rowData.value[this.n]={
            Col2: 'RESULT', 
            Col3:'Total: '+sumCol3.toFixed(2) + 'kg', 
            Col4:'Average: '+ (middleCol4/this.n).toFixed(0), 
      }
    },
    showInfo(event){
      let row;
      if( !(event.target.tagName == "IMG"))
      { row=event.target.parentNode?event.target.parentNode:0;}
      else{ 
        row=event.target.parentNode.parentNode?event.target.parentNode.parentNode:0;
      }
      if (row.classList.contains('ag-row')){
        let rowObj={};
        for (let i=0;i<row.childNodes.length;i++){
            let field=row.childNodes[i].attributes[4].textContent;
            if (field && row.childNodes[i].childNodes[0]){
              rowObj[field]=row.childNodes[i].childNodes[0].nodeValue?row.childNodes[i].childNodes[0].nodeValue:row.childNodes[i].childNodes[0].src;
            } 
        }
        alert(JSON.stringify(rowObj));
      }
    }
  },
  mounted() {
    this.createAndRenderData();
  },  
};
</script>