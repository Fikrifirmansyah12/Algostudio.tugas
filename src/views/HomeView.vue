<template>
  <div class="home">
    <h1>Penilaian Karyawan</h1>

    <div class="form-group">
      <label for="karyawan">Nama Karyawan:</label>
      <select class="option" v-model="selectedKaryawan">
        <option disabled value="">Pilih karyawan</option>
        <option v-for="karyawan in karyawans" :key="karyawan" :value="karyawan">{{ karyawan }}</option>
      </select>  
    </div>

    <table class="penilaian-table">
      <thead>
        <tr>
          <th>No</th>
          <th>Kriteria Penilaian</th>
          <th>Nilai (1-5)</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(nilai, index) in penilaian" :key="index">
          <td>{{ index + 1 }}</td>
          <td>{{ nilai.kriteria }}</td>
          <td><input type="number" v-model.number="nilai.score" min="1" max="5" @input="validateScore(index)" /></td>
        </tr>
      </tbody>
    </table>

    <div class="button-group">
      <button @click="resetForm" class="btn">Reset</button>
      <button @click="pratinjau" class="btn">Pratinjau</button>
    </div>

    <div v-if="showPreview" class="preview" id="print-area">
      <div class="employee-info">
        <div class="employee-details">
          <p><strong>Nama :</strong> {{ selectedKaryawan }}</p>
          <p><strong>Posisi :</strong> Developer</p>
          <p><strong>Alamat :</strong> Jl.Soekarno Hatta, No. A17, Lowokwaru-Malang</p>
        </div>
        <br /><br />
        <div class="penilaian-header">
          <p><strong>Daftar Penilaian</strong></p>
        </div>
      </div>

      <ul class="penilaian-list">
        <li v-for="(nilai, index) in penilaian" :key="index" class="penilaian-item">
          <div>
            {{ index + 1 }}. {{ nilai.kriteria }}
          </div>
          <div class="stars">
            {{ renderStars(nilai.score) }}
          </div>
        </li>
      </ul>

      <div class="total-score">
        <p><strong>Total Persentase:</strong> {{ persentase }}%</p>
        <p><strong>Grade:</strong> {{ grade }}</p>
      </div>
    </div>

    <button v-if="showPreview" @click="downloadPDF" class="btn">Download</button>
  </div>
</template>

<script>
import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';

export default {
  data() {
    return {
      selectedKaryawan: '',
      karyawans: ['Fikri Haikal', 'Eko Wahyudi', 'Wahyu Nugroho'],
      penilaian: [
        { kriteria: 'Mengerti tentang Bahasa pemrograman PHP', score: null },
        { kriteria: 'Mengerti tentang Bahasa pemrograman HTML & Javascript', score: null },
        { kriteria: 'Pernah menggunakan sistem database (MySQL, SQL Server, PostgreSQL)', score: null },
        { kriteria: 'Paham konsep OOP (Object-Oriented Programming)', score: null },
        { kriteria: 'Paham konsep dasar system Git', score: null },
        { kriteria: 'Pengalaman menggunakan framework web (Laravel, CodeIgniter, Vue.js, Express, dll)', score: null },
        { kriteria: 'Mampu bekerja secara tim dengan baik', score: null },
        { kriteria: 'Lancar berbahasa Inggris', score: null }
      ],
      showPreview: false,
    };
  },
  computed: {
    totalScore() {
      return this.penilaian.reduce((total, nilai) => total + (nilai.score || 0), 0);
    },
    persentase() {
      const maxScore = this.penilaian.length * 5;
      const persentaseValue = (this.totalScore / maxScore) * 100;
      return Math.round(persentaseValue);
    },
    grade() {
      const persen = this.persentase;
      if (persen >= 90) return 'A';
      if (persen >= 70) return 'B';
      if (persen >= 50) return 'C';
      if (persen >= 25) return 'D';
      if (persen >= 6) return 'E';
      return 'F';
    }
  },
  methods: {
    resetForm() {
      this.selectedKaryawan = '';
      this.penilaian.forEach(nilai => (nilai.score = null));
      this.showPreview = false;
    },
    pratinjau() {
      const allFilled = this.penilaian.every(nilai => nilai.score !== null);
      if (this.selectedKaryawan && allFilled) {
        this.showPreview = true;
      } else {
        alert('Mohon isi semua inputan sebelum melihat pratinjau');
      }
    },
    validateScore(index) {
      if (this.penilaian[index].score < 1) this.penilaian[index].score = 1;
      if (this.penilaian[index].score > 5) this.penilaian[index].score = 5;
    },
    renderStars(score) {
      const filledStar = '\u2605';
      return filledStar.repeat(score);
    },
    downloadPDF() {
      const printArea = document.getElementById('print-area');
      html2canvas(printArea, { scale: 2 }).then(canvas => {
        const imgData = canvas.toDataURL('image/png');
        const pdf = new jsPDF({
          orientation: 'portrait',
          unit: 'mm',
          format: 'a4'
        });

        const imgWidth = 210;
        const imgHeight = (canvas.height * imgWidth) / canvas.width;
        const pageHeight = 297;
        let position = 0;

        pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);

        if (imgHeight >= pageHeight) {
          let remainingHeight = imgHeight;
          while (remainingHeight >= pageHeight) {
            pdf.addPage();
            pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
            remainingHeight -= pageHeight;
          }
        }

        pdf.save('penilaian-karyawan.pdf');
      });
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #ffffff;
}
.form-group label {
  display: flex;
  flex-direction: column;
  align-items: flex-start; 
}
h1 {
  text-align: center;
  color: #333;
}

.penilaian-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.penilaian-table th, .penilaian-table td {
  border: 1px solid #ccc;
  padding: 8px;
}

.button-group {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}

.btn {
  padding: 10px 20px;
  background-color: #1a02a1;
  color: white;
  border: none;
  cursor: pointer;
  margin-left: 10px;
}

.preview {
  margin-top: 20px;
  background-color: #ffffff;
  padding: 20px;
  border-radius: 5px;
}

.employee-info {
  margin-bottom: 20px;
}

.employee-details {
  margin-bottom: 20px;
  text-align: left; 
}

.penilaian-header {
  margin-bottom: 20px;
  text-align: left; 
}

.penilaian-list {
  list-style-type: none;
  padding: 0;
}

.penilaian-item {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  align-items: flex-start; 
}

.stars {
  margin-top: 5px; 
  color: gold;
  font-size: 20px; 
}

.total-score {
  margin-top: 20px;
  text-align: right; 
}

.total-score p {
  font-size: 18px;
}

.option {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

@media print {
  body {
    margin: 0;
    padding: 0;
  }

  #print-area {
    width: 210mm; 
    height: 297mm; 
    padding: 20mm; 
    box-sizing: border-box;
  }

  .employee-info {
    margin-bottom: 20px;
  }

  .penilaian-list {
    margin-top: 20px;
    page-break-inside: avoid; 
  }
}
</style>
