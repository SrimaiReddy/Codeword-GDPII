/**
 * @author Naveen kumar Chandaluri
 */
<template>
    <div id="codeWord" class="container-fluid" style="margin-top:5em">
    <div class="col-md-12 col-lg-12 col-xs-12 col-sm-12">
      <h3>Codeword set Name: <strong>{{ CodeWordSetName }}</strong></h3>
      <button type="button" class="btn btn-info btn-sm" data-toggle="modal" data-target="#addcodeword" style="marging-left:10px">
  <i class="fas fa-fa-plus"></i>
</button>
<div class="modal fade" id="addcodeword" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Add Codeword</h5>  
        <button type="button" class="close" data-dismiss="modal" aria-label="Cancel">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        Enter new codeword: <input type="text" v-model="newCodeword">
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" data-dismiss="modal" @click="addCodeword()">Add Codeword</button>
        <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
      </div>
    </div>
  </div>
</div>

<div class="col-md-4 col-lg-4 col-xs-4 col-sm-4 headingstyle">
<!-- Modal -->
<div class="modal fade" id="createcodeword" tabindex="-1" role="dialog" aria-labelledby="createcodewordLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="createcodewordLabel">Add Codeword</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
<div class="form-group text-left">
      Upload Cordwords(Excel)
      <input type="file"  name="file" ref="myFile"  class="form-control-file" id="exampleFormControlFile1" style="margin-top:.2em" required>
        </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary">Add</button>
      </div>
    </div>
  </div>
</div>
</div>
<table class="table">
                <thead class="thead-dark">
                    <tr>
                        <th scope="col">#</th>
                        <th scope="col">Codewords</th>
                        <th scope="col">Options</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(codeword,index) in codewords" :key="codeword._id">
                        <td scope="row">{{ index+1 }}</td>
                        <td > {{ codeword }}</td>
                        <td>
                          <button type="button" class="btn btn-info btn-sm" data-toggle="modal" @click="selectCodeword(index)" data-target="#editcodeword" style="marging-left:10px">
                             <i class="fas fa-pencil-alt"></i>
                        </button> 
                        <button type="button" class="btn btn-info btn-sm" data-toggle="modal" data-target="#deletecodeword" @click="selectCodeword(index)" style="marging-left:10px">
                             <i class="fas fa-trash"></i>
                        </button>
<!-- Modal Edit Course -->
<div class="modal fade" id="editcodeword" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Update Codeword</h5>  
        <button type="button" class="close" data-dismiss="modal" aria-label="Cancel">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        Codeword: <input type="text" v-model="selectedCodeword">
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-primary" data-dismiss="modal" @click="editCodeword(selectedCodeword, codewordIndex)">Update Codeword</button>
        <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
      </div>
    </div>
  </div>
</div>
      <!-- Modal Delete codeword -->
<div class="modal fade" id="deletecodeword" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Delete Codeword</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Cancel">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <h1> {{selectedCodeword}} </h1>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-danger" data-dismiss="modal">Cancel</button>
        <button type="button" class="btn btn-primary" data-dismiss="modal" @click="deleteCodeword">Delete Codeword</button>
      </div>
    </div>
  </div>
</div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    </div>
</template>
<script>
/* global axios */

export default {
  name: 'CodeWordSet',
  data () {
    return {
      CodeWordSetName: '',
      codewords: [],
      selectedCodeword: '',
      codewordIndex: '',
      newCodeword: ''
    }
  },
  created () {
    if (this.$route.params.CodeWordSetName == null) {
      this.CodeWordSetName = window.localStorage.getItem('setId')
      this.getCodeWords()
    } else {
      window.localStorage.setItem('setId', this.$route.params.CodeWordSetName)
      this.CodeWordSetName = this.$route.params.CodeWordSetName
      this.getCodeWords()
    }
  },
  methods: {
    getCodeWords () {
      axios({
        method: 'post',
        url: '/codeword/getCodewords',
        data: {
          CodeWordSetKey: this.CodeWordSetName
        },
        headers: {
          token: window.localStorage.getItem('token')
        }
      }).then(response => {
        this.codewords = response.data.data
      })
    },
    selectCodeword (index) {
      this.codewordIndex = index
      this.selectedCodeword = this.codewords[index]
    },
    editCodeword (selectedCodeword, index) {
      this.codewords[this.codewordIndex] = selectedCodeword
      this.updateCodeword()
    },
    deleteCodeword () {
      this.codewords.splice(this.codewordIndex, 1)
      this.updateCodeword()
    },
    addCodeword () {
      this.codewords.push(this.newCodeword)
      this.updateCodeword()
    },
    updateCodeword () {
      axios({
        method: 'post',
        url: '/codeword/updatecodeword',
        data: {
          CodeWordSetKey: this.CodeWordSetName,
          updatedCodewords: this.codewords
        },
        headers: {
          token: window.localStorage.getItem('token')
        }
      }).then(response => {
        this.getCodeWords()
      })
    },
    selectCodewordSet (setName) {
      this.CodewordSetName = setName
    }
  }
}
</script>
<style>
.headingstyle {
  font-weight: bold;
}
</style>
