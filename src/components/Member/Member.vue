<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>
        <div class="container-fluid">
          <!-- Page Heading -->
          <h1 class="h3 mb-2 text-gray-800">MEMBER</h1>
          <!-- DataTales Example -->
          <div class="card shadow mb-4">
            <div class="card-header py-3">
              <h6 class="m-0 font-weight-bold text-primary">Data Member</h6>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table
                  class="table table-bordered"
                  id="dataTable"
                  width="100%"
                  cellspacing="0"
                >
                  <thead>
                    <tr>
                      <th>NO</th>
                      <th>NAMA</th>
                      <th>ALAMAT</th>
                      <th>JENIS KELAMIN</th>
                      <th>TELP</th>
                      <th>AKSI</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(m, index) in member" :key="index">
                      <td>{{ index + 1 }}</td>
                      <td>{{ m.nama }}</td>
                      <td>{{ m.alamat }}</td>
                      <td>{{ m.jk }}</td>
                      <td>{{ m.telp }}</td>
                      <td>
                        <router-link
                          :to="{
                            name: 'detail_member',
                            params: { id: m.id },
                          }"
                          class="btn btn-success btn-icon-split"
                        >
                          <span class="icon text-white-50">
                            <i class="fas fa-eye"></i>
                          </span>
                          <span class="text">Detail</span>
                        </router-link>
                        &nbsp;
                        <router-link
                          :to="{
                            name: 'edit_member',
                            params: { id: m.id },
                          }"
                          class="btn btn-warning btn-icon-split"
                        >
                          <span class="icon text-white-50">
                            <i class="fas fa-pen"></i>
                          </span>
                          <span class="text">Edit</span>
                        </router-link>
                        &nbsp;
                        <button
                          type="submit"
                          @click="alertDisplay(m.id)"
                          class="btn btn-danger btn-icon-split"
                        >
                          <span class="icon text-white-50">
                            <i class="fas fa-trash"></i>
                          </span>
                          <span class="text">Delete</span>
                        </button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div>
                <router-link
                  to="/member/tambah"
                  class="btn btn-info btn-icon-split"
                >
                  <span class="icon text-white-50">
                    <i class="fas fa-plus"></i>
                  </span>
                  <span class="text">Tambah</span>
                </router-link>
              </div>
            </div>
          </div>
        </div>
      </div>
      <footer-component></footer-component>
    </div>
  </div>

  <!-- Begin Page Content -->
</template>
<script>
export default {
  data() {
    return {
      member: {},
    };
  },
  created() {
        var data = JSON.parse(this.$store.state.datauser)
        var role = data.role
        if(role == 'owner')
        {
            this.$swal("Anda tidak dapat mengakses halaman ini")
            this.$router.push('/') 
        }

    this.axios
      .get("http://localhost/api-laundry/public/api/member", {
        headers: { Authorization: "Bearer " + this.$store.state.token },
      })
      .then((res) => {
        this.member = res.data;
      });
  },
  methods: {
    hapus(id) {
      this.axios
        .delete(`http://localhost/api-laundry/public/api/member/${id}`, {
          headers: { Authorization: "Bearer " + this.$store.state.token },
        })
        .then((res) => {
          if(res.data.success){
            let i = this.member.map((item) => item.id).indexOf(id);
            this.member.splice(i, 1);
            // this.$swal("Sukses", res.data.message, "success")
            this.$swal(res.data.message)
          }else{
            this.$swal('Data member gagal dihapus')
          }
        }).catch()
    },
    alertDisplay(id) {
      this.$swal({
        title: "Apa sudah yakin?",
        text: "Data tidak bisa kembali jika terhapus",
        type: "warning",
        showCancelButton: true,
        confirmButtonText: "Yakin",
        cancelButtonText: "Tidak, tunggu",
        showCloseButton: true,
        showLoaderOnConfirm: true,
      }).then((result) => {
        if (result.value) {
          this.$swal(
            "Sukses",
            "Data Member Berhasil Dihapus",
            "success",
            
            this.axios
              .delete(
                `http://localhost/api-laundry/public/api/member/${id}`,
                {
                  headers: {
                    Authorization: `Bearer ` + this.$store.state.token,
                  },
                }
              )
              .then(() => {
                let i = this.member
                  .map((item) => item.id)
                  .indexOf(id);
                this.member.splice(i, 1);
              })
          );
        //}else {
        //   this.$swal("Cancelled", "Your Data Is Still Intact", "info");
        }
      });
    },
  },
};
</script>
