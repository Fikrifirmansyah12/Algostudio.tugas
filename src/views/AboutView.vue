<template>
  <div class="about">
    <div ref="content" class="container">
      <h1>Form Pengajuan Izin Cuti</h1>
      <form @submit.prevent="handleSubmit" class="form">
        <div class="form-group">
          <label for="name">Nama:</label>
          <input type="text" id="name" v-model="form.name" required />
        </div>

        <div class="form-group">
          <label for="jeniscuti">Jenis Cuti:</label>
          <select id="jeniscuti" v-model="form.jeniscuti" required>
            <option disabled value="">Pilih Jenis Cuti</option>
            <option value="Cuti Tahunan">Cuti Tahunan</option>
            <option value="Cuti Sakit">Cuti Sakit</option>
            <option value="Cuti Melahirkan">Cuti Melahirkan</option>
            <option value="Cuti Besar">Cuti Besar</option>
            <option value="Cuti Alasan Penting">Cuti Alasan Penting</option>
          </select>
        </div>

        <div class="form-group">
          <label for="berapahari">Jumlah Hari:</label>
          <input type="text" id="berapahari" v-model="form.berapahari" required />
        </div>

        <div class="form-group">
          <label for="awalcuti">Tanggal Mulai:</label>
          <input type="date" id="awalcuti" v-model="form.awalcuti" required />
        </div>

        <div class="form-group">
          <label for="akhircuti">Tanggal Selesai:</label>
          <input type="date" id="akhircuti" v-model="form.akhircuti" required />
        </div>

        <div class="form-group">
          <label for="alasan">Alasan:</label>
          <textarea id="alasan" v-model="form.alasan" required></textarea>
        </div>

        <div class="form-group">
          <button @click.prevent="download" class="btn">Download PDF</button>
        </div>

        <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
      </form>
    </div>
  </div>
</template>

<script>
import jsPDF from 'jspdf';

export default {
  data() {
    return {
      form: {
        name: '',
        jeniscuti: '',
        berapahari: '',
        awalcuti: '',
        akhircuti: '',
        alasan: ''
      },
      errorMessage: ''
    };
  },
  methods: {
    // Fungsi untuk mengonversi tanggal ke format yang diinginkan
    formatDate(dateString) {
      const months = [
        'Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni',
        'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'
      ];
      const date = new Date(dateString);
      const day = date.getDate();
      const month = months[date.getMonth()];
      const year = date.getFullYear();
      return `${day} ${month} ${year}`;
    },

    download() {
      this.errorMessage = '';
      for (const key in this.form) {
        if (!this.form[key]) {
          this.errorMessage = 'Semua kolom harus diisi. Harap lakukan peninjauan kembali.';
          return;
        }
      }

      const doc = new jsPDF();
      const currentDate = new Date();
      const formattedDate = currentDate.toLocaleDateString('id-ID', {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      });

      // Gunakan formatDate untuk tanggal mulai dan selesai
      const startDate = this.formatDate(this.form.awalcuti);
      const endDate = this.formatDate(this.form.akhircuti);

      doc.setFontSize(12);
      doc.text(40, 20, `SURAT IZIN CUTI`);
      doc.setFontSize(10);
      doc.text(20, 40, `Yang bertanda tangan di bawah ini :`);
      doc.text(20, 50, `Nama: ${this.form.name}`);
      doc.text(20, 60, `Jenis Cuti: ${this.form.jeniscuti}`);
      doc.text(20, 70, `Berapa Hari: ${this.form.berapahari}`);
      doc.text(20, 80, `Tanggal Mulai: ${startDate}`);
      doc.text(20, 90, `Tanggal Selesai: ${endDate}`);
      doc.text(20, 100, `Alasan cuti: ${this.form.alasan}`);
      doc.text(20, 110, `Demikian surat izin cuti ini saya buat dengan sebenar-benarnya.`);
      doc.text(20, 120, `Saya berharap izin ini dapat disetujui.`);
      doc.text(20, 140, `Malang, ${formattedDate}`);
      doc.text(20, 150, `Hormat Saya`);
      doc.text(20, 170, ` ${this.form.name}`);
      
      doc.save('Surat-cuti-pegawai.pdf');
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
}

.form {
  margin-top: 20px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

input, textarea, select {
  width: 100%;
  padding: 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

input:focus, textarea:focus, select:focus {
  border-color: #7990df;
  outline: none;
}

textarea {
  resize: vertical;
}

.btn {
  padding: 10px 15px;
  background-color: #1a02a1;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
  width: 100%;
}

.btn:hover {
  background-color: #1b7cbd;
}

.error-message {
  color: red;
  margin-top: 15px;
  text-align: center;
  font-weight: bold;
}
</style>
