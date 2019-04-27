<template>
    <div>
     <el-row>
        <el-col :span="12">
            <el-text>Pilih tipe inputan</el-text> <br><br>
            <el-radio v-model="radio"  label="1">Masuk</el-radio>
            <el-radio v-model="radio"  label="2">Keluar</el-radio>  
        </el-col>  
        <el-col :span="12">
            <el-text>Pilih Gudang</el-text> <br><br>
             <el-select v-model="value" placeholder="Select">
              <el-option
                v-for="item in options"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
        </el-col>  
     </el-row> 
     <el-divider></el-divider>
    
     <el-row>
        <el-col :span="20">
            <el-input placeholder="Please input" v-model="input" ></el-input>
        </el-col>  
        <el-col :span="4">   
            <div v-if="radio == 1">              
              &nbsp;<el-button  @click="submitForm">Submit</el-button> 
            </div>         
            <div v-else-if="radio == 2">              
              &nbsp;<el-button>Opps</el-button> 
            </div>         
            <div v-else>              
              &nbsp;<el-button  @click="submitForm">Submit</el-button> 
            </div>         
        </el-col>  
     </el-row>
     <el-divider></el-divider> 
     <el-row>
        <el-col :span="11">
          <h1>Barang Inputan</h1>
         <el-table
            :data="tableDataInput"
            stripe
            style="width: 100%">
            <el-table-column
              prop="name"
              label="Nama Barang Input"
              width="180">
            </el-table-column>            
            <el-table-column
              prop="gudang.name"
              label="Gudang"
              width="180">
            </el-table-column>   
            <el-table-column 
              label="Operations">
              <template slot-scope="scope" v-if="radio == 2">
               
                <el-button
                  size="mini"
                  type="danger"
                  @click="handleDelete(scope.$index, scope.row)">Out</el-button>
              </template>
            </el-table-column>         
          </el-table>
         </el-col>
         <el-col :span="2">
           &nbsp;&nbsp;
         </el-col>
        <el-col :span="11">
          <h1>Barang ouput</h1>
         <el-table
            :data="tableDataOutput"
            stripe
            style="width: 100%">
            <el-table-column
              prop="name"
              label="Nama Barang Output"
              width="180">
            </el-table-column>            
            <el-table-column
              prop="gudang.name"
              label="Gudang"
              width="180">
            </el-table-column>   
            <el-table-column 
              label="">
              <template>
          
              </template>
            </el-table-column>           
          </el-table>
         </el-col>
    </el-row>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    mounted(){
      let self = this;
      axios.get(self.base_url+"/gudangs")
            .then((res)=>{
               self.options = res.data;
            })
            .catch((err)=>{

            });

      self.getData(1); 
      self.getData(2);      

    },
    data(){
      return{
        base_url:'http://localhost:8000/api',
        radio:'true',
        input:"",
        options:[],
        value:'',
        tableDataInput: [],
        tableDataOutput: []
      }
    },
    methods:{     
      handleDelete(index, row) {
        let self = this;
        // console.log(index, row);
        axios({
              url:self.base_url+"/barang/"+row.id+"/update",
              method: 'post',
              data:{      
                name:self.input,
                gudang_id:self.value,
                param:self.radio
              }
            })
            .then((res)=>{
             self.tableDataOutput = res.data;  
             self.getData(2);  
             self.getData(1);     
             self.open("Data berhasil di output","Success");
            })
            .catch((err)=>{
              self.open("Terjasi kesalahan","Warning");
            });
      },
      submitForm(){
        let self = this;
        if(self.radio == "true"){
          self.open("Silahkan pilih tipe inputan","Warning");
        }else if(self.value == '' || self.value == null ){
          self.open("Silahkan pilih Gudang","Warning");
        }else if(self.input == "" || self.input == null){
          self.open("Silahkan isi nama barang","Warning");
        }else{         
            axios({
              url:self.base_url+"/barang",
              method: 'post',
              data:{       
                
                name:self.input,
                gudang_id:self.value,
                param:self.radio
              }
            })
            .then((res)=>{
            //  console.log() 
            //  self.tableDataInput = res.data; 
              self.open("Data berhasil di input","Success");
              self.value = '';
              self.radio = 'true';
              self.input = '';
              self.getData(1);
            })
            .catch((err)=>{
              self.open("Terjasi kesalahan","Warning");
            });

            
            
        }
      },
      open(message,title) {
        this.$alert(message, title, {
          confirmButtonText: 'OK'          
        });
      },
      getData(param){
        let self = this;
        axios.get(self.base_url+"/barang/get/"+param)
          .then((res)=>{
            if(param == 1){
              self.tableDataInput = res.data;
            }else if(param == 2){
              self.tableDataOutput = res.data;
            }
          })
          .catch((err)=>{
            console.log(err);
          })
      }
    }
}
</script>
