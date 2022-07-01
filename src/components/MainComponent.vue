<template>
    <div class="container">    
        <div class="row">
            <div class="col-12 mt-3 text-center">
                <!-- Styled -->
                <b-form-file
                    v-model="file1"
                    :state="Boolean(file1)"
                    placeholder="Choose a file or drop it here..."
                    drop-placeholder="Drop file here..."
                    ref="file-input"
                ></b-form-file>
            </div>
            <div class="col-4 mt-3 text-center">
                <b-button @click="UploadFile" class="mr-2" :disabled="file1 ? false : true">Upload</b-button>
            </div>
            <div class="col-4 mt-3 text-center">
                <b-button @click="file1 = null" :disabled="file1 ? false : true">Clear</b-button>
            </div>
            <div class="col-4 mt-3 text-center" v-show="info">
                <b-button @click="exportTableToExcel('tblData')" :disabled="file1 ? false : true">Download</b-button>
            </div>
            <div class="col-12 mt-3">
                <div class="mt-3">Selected file: {{ file1 ? file1.name : '' }}</div>
            </div>
            <div class="col-12" v-show="info">
                <table class="table" id="tblData">
                    <thead>
                        <tr>
                            <th scope="col">well</th>
                            <th scope="col">target id</th>
                            <th scope="col">target name</th>
                            <th scope="col">ct</th>
                            <th scope="col">amp</th>
                            <th scope="col">cq</th>
                            <th scope="col">result</th>
                            <th scope="col">comment</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="data in info" :key="data.result.well">
                            <th scope="row">{{data.result.well}}</th>
                            <td>{{data.result['target id']}}</td>
                            <td>{{data.result['target name']}}</td>
                            <td :class="data.status.ct == 1 ? 'bg-danger' : ''">{{data.result.ct}}</td>
                            <td :class="data.status.amp == 1 ? 'bg-danger' : ''">{{data.result.amp}}</td>
                            <td :class="data.status.cq == 1 ? 'bg-danger' : ''">{{data.result.cq}}</td>
                            <td>{{data.status.status == 1 ? 'POS' : 'NEG'}}</td>
                            <td>{{data.status.status == 1 ? data.result.comment : ''}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    data() {
      return {
        file1: null,
        info: null
      }
    },
    methods: {
        UploadFile() {
            axios.post('http://127.0.0.1:8000/csv/',
            {
                file:this.file1
            },
            {
                headers: {
                    "Content-Type": "multipart/form-data",
                }
            }
            ).then(response => {
                this.info = response.data.file_contents
            })
        },
        exportTableToExcel(table){
            // Define your style class template.
            var style = "<style>.bg-danger { background-color: red; }</style>";
            var uri = 'data:application/vnd.ms-excel;base64,'
                , template = '<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:x="urn:schemas-microsoft-com:office:excel" xmlns="http://www.w3.org/TR/REC-html40"><head><!--[if gte mso 9]><xml><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet><x:Name>{worksheet}</x:Name><x:WorksheetOptions><x:DisplayGridlines/></x:WorksheetOptions></x:ExcelWorksheet></x:ExcelWorksheets></x:ExcelWorkbook></xml><![endif]-->' + style + '</head><body><table>{table}</table></body></html>'
                , base64 = function (s) {
                    return window.btoa(unescape(encodeURIComponent(s)))
                }
                , format = function (s, c) {
                    return s.replace(/{(\w+)}/g, function (m, p) { return c[p]; })
                }
                if (!table.nodeType) table = document.getElementById(table)
                var ctx = { worksheet: name || 'Worksheet', table: table.innerHTML }
                window.location.href = uri + base64(format(template, ctx))
        }
    }
}
</script>

<style>

</style>