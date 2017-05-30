<template>
    <div>
        <div>
            <el-dialog v-bind:title="title" :visible.sync="dialogFormVisible">
              <el-form :model="form">
                <el-form-item label="单据编号" :label-width="formLabelWidth">
                  <el-input v-model="form.billNumber" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="付款状态" :label-width="formLabelWidth">
                  <el-select v-model="form.paymentStatus" placeholder="请选择活动区域">
                    <el-option label="已付款" value="1"></el-option>
                    <el-option label="未付款" value="2"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="收款员工" :label-width="formLabelWidth">
                  <el-input v-model="form.supplierId" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="日期" :label-width="formLabelWidth">
                  <el-date-picker v-model="form.date" type="date" placeholder="选择日期">
                  </el-date-picker>
                </el-form-item>
                <el-form-item label="数额" :label-width="formLabelWidth">
                  <el-input-number v-model="form.count" @change="handleChange" :min="1" :max="1000"></el-input-number>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button type="primary" @click="save()">确 定</el-button>
                <el-button @click="dialogFormVisible = false">取 消</el-button>
              </div>
            </el-dialog>
        </div>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
             <el-button type="primary" @click="add()" size="small" icon="plus"></el-button>
        <el-table :data="thisdata" border style="width: 100%;">
            <el-table-column type="selection" width="70"></el-table-column>
            <el-table-column prop="billNumber" label="单据编号">
            </el-table-column>
            <el-table-column prop="paymentStatus" label="付款状态" >
            </el-table-column>
            <el-table-column prop="supplierId" label="收款员工">
            </el-table-column>
            <el-table-column prop="date" label="日期" :formatter="dateFormat">
            </el-table-column>
            <el-table-column prop="count" label="数额">
            </el-table-column>
            <el-table-column label="操作" width="180">
                <template scope="scope">
                    <el-button size="small" icon="edit"
                            @click="handleEdit(scope.$index, scope.row)"></el-button>
                    <el-button size="small" type="danger" icon="delete"
                            @click="handleDelete(scope.$index, scope.row)"></el-button>
                </template>
            </el-table-column>
        </el-table>
         <div class="pagination">
            <el-pagination
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination>
        </div> 
    </div>
    </div>
</template>

<script>
import API from '../../api/API'
const api = new API();
var moment = require('moment');
    export default {
        beforeMount(){
            this.loading=true;
            let that=this;
            let params={
                api:"/core/api/test/payment/query?access_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsiYXBpLXJlc291cmNlIl0sInVzZXJfbmFtZSI6IkFETUlOIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIiwidHJ1c3QiXSwiZXhwIjoxNDk2MTM2MjIyLCJhdXRob3JpdGllcyI6WyJST0xFX1VTRVIiLCJFTVBMT1lFRSIsIkFETUlOIl0sImp0aSI6IjgwYTQzN2M0LWMzZWEtNDQxMC1iNTBiLTkyODE3ZmQ3MTBjNSIsImNsaWVudF9pZCI6ImNsaWVudDIifQ.owM2UjqbrRZPpN7iPKCFn4iGtTvwkBjvS8ClIuuJTHU",
                param:"",
            };
            api.post(params)
                .then(function(res){
                    that.thisdata = res.data.rows;//渲染数据到grid
                    //console.log(res.data.rows);
                })
                .catch(function(err){
                    console.log(err);
                })
        },
        data() {
            return {
                thisdata:null,//grid数据
                /*弹出框 begin*/
                dialogFormVisible: false,
                title:"",
                        form: {
                          billNumber: '',
                          paymentStatus: '',
                          supplierId: '',
                          date: '',
                          count: ''
                        },
                        formLabelWidth: '120px',
                }

        },

        methods: {
            //grid保存
            save(){
                this.dialogFormVisible=false,
                this.loading=true;
                let that=this;
                let data;
                let dataList=[];
                data={
                        "billNumber":this.form.billNumber,
                        "paymentStatus":this.form.paymentStatus,
                        "supplierId":this.form.supplierId,
                        "date":this.form.date,
                        "count":this.form.count,
                        "paymentId":this.form.paymentId,
                        "__status":""
                    };
                if(this.title=="新增"){
                    data.__status="add";
                }else if(this.title=="编辑"){
                    data.__status="update";
                }
                dataList.push(data);
                let params={
                    api:"/core/api/test/payment/submit?access_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsiYXBpLXJlc291cmNlIl0sInVzZXJfbmFtZSI6IkFETUlOIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIiwidHJ1c3QiXSwiZXhwIjoxNDk1ODczNDAxLCJhdXRob3JpdGllcyI6WyJST0xFX1VTRVIiLCJFTVBMT1lFRSIsIkFETUlOIl0sImp0aSI6IjYzNDUyMjE4LTdmYjQtNDlhNS04YWQ3LTExNDg3YzcxM2Q5OCIsImNsaWVudF9pZCI6ImNsaWVudDIifQ.Q3-Nl5lKmzMonGrLynn1s-FU37-fQUOzZe8fqFgN0V4",
                    param:JSON.stringify(dataList),
                };
                api.post(params)
                    .then(function(res){ 
                         window.location.reload();
                    })
                    .catch(function(err){
                        console.log(err);
                    })
            },
            //时间格式化  
            dateFormat(row, column) {  
               var date = row[column.property];  
               if (date == undefined) {  
                   return "";  
                }  
                return moment(date).format("YYYY-MM-DD");  
            }, 
            add(){
                this.dialogFormVisible=true;
                this.title="新增";
            },
            //grid编辑
            handleEdit(index, row) {
                this.dialogFormVisible=true;
                this.title="编辑";
                this.form.billNumber=row.billNumber;
                this.form.paymentStatus=row.paymentStatus;
                this.form.supplierId=row.supplierId;
                this.form.date=row.date;
                this.form.count=row.count;
                this.form.paymentId=row.paymentId;
            },
            handleDelete(index, row) {
                let data;
                let dataList=[];
                data={
                        "paymentId":row.paymentId,
                        "__status":"delete"
                    };
                dataList.push(data);
                this.loading=true;
                let that=this;
                let params={
                    api:"/core/api/test/payment/submit?access_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsiYXBpLXJlc291cmNlIl0sInVzZXJfbmFtZSI6IkFETUlOIiwic2NvcGUiOlsicmVhZCIsIndyaXRlIiwidHJ1c3QiXSwiZXhwIjoxNDk2MTM2MjIyLCJhdXRob3JpdGllcyI6WyJST0xFX1VTRVIiLCJFTVBMT1lFRSIsIkFETUlOIl0sImp0aSI6IjgwYTQzN2M0LWMzZWEtNDQxMC1iNTBiLTkyODE3ZmQ3MTBjNSIsImNsaWVudF9pZCI6ImNsaWVudDIifQ.owM2UjqbrRZPpN7iPKCFn4iGtTvwkBjvS8ClIuuJTHU",
                    param:JSON.stringify(dataList),
                };
                api.post(params)
                    .then(function(res){
                        window.location.reload();
                    })
                    .catch(function(err){
                        console.log(err);
                    })
            },
            handleChange(value) {
            }
        }

    }
</script>