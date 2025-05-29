<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Form with Bootstrap</title>
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet" />
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.5.3/dist/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@7.12.15/dist/sweetalert2.all.min.js"></script>
    <style>
      body {
        background-color: #e9f5e9;
      }
      .form-container {
        max-width: 700px;
        margin: auto;
        margin-top: 50px;
        padding: 20px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        background-color: #ffffff;
        border-radius: 8px;
      }
      .form-control {
        border-radius: 5px;
      }
      .btn-primary {
        background-color: #28a745;
        border-color: #28a745;
      }
      .btn-primary:hover {
        background-color: #218838;
        border-color: #1e7e34;
      }
      .form-group label {
        font-weight: bold;
      }
      .row {
        display: flex;
        align-items: stretch;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="form-container">
        <h4 class="text-center mb-4">Form Pendaftaran PPDB Candle Tree</h4>
        <form id="submit-to-google-sheet">
          <div class="row">
            <div class="col-md-6">
              <div class="form-group">
                <label for="name">Nama Lengkap:</label>
                <input class="form-control" type="text" name="name" id="name" placeholder="Nama Lengkap Sesuai Akte Lahir" required />
              </div>
              <div class="form-group">
                <label for="tahun_akademik">Tahun Akademik:</label>
                <select class="form-control" name="tahun_akademik" id="tahun_akademik" required>
                  <option value="" disabled selected hidden>Tahun Akademik</option>
                  <option value="2025/2026">2025/2026</option>
                  <option value="2026/2027">2026/2027</option>
                  <option value="2027/2028">2027/2028</option>
                </select>
              </div>
              <div class="form-group">
                <label for="jenjang">Pilih Jenjang:</label>
                <select class="form-control" name="jenjang" id="jenjang" required>
                  <option value="" disabled selected hidden>Jenjang</option>
                  <option value="SD">SD</option>
                  <option value="SMP">SMP</option>
                  <option value="SMA">SMA</option>
                </select>
              </div>
              <div class="form-group">
                <label for="kelas">Kelas:</label>
                <select class="form-control" name="kelas" id="kelas" required disabled>
                  <option value="" disabled selected hidden>Pilih Jenjang Terlebih Dahulu</option>
                </select>
              </div>
              <div class="form-group">
                <label for="sekolah">Sekolah Asal:</label>
                <input class="form-control" type="text" name="sekolah" id="sekolah" placeholder="Sekolah Asal" required />
              </div>
            </div>
            <div class="col-md-6">
              <div class="form-group">
                <label for="email">Email:</label>
                <input class="form-control" type="email" name="email" id="email" placeholder="Email" required />
              </div>
              <div class="form-group">
                <label for="whatsapp">No WhatsApp:</label>
                <input class="form-control" type="tel" name="whatsapp" id="whatsapp" placeholder="No WhatsApp" required />
              </div>
              <div class="form-group">
                <label for="metode_pembayaran">Metode Pembayaran:</label>
                <select class="form-control" name="metode_pembayaran" id="metode_pembayaran" required>
                  <option value="" disabled selected hidden>Pembayaran formulir dengan cara</option>
                  <option value="Qris">Qris</option>
                  <option value="Transfer">Transfer</option>
                  <option value="Debit">Debit</option>
                </select>
              </div>
              <div class="form-group mt-2">
                <label for="media" class="form-check-label">Upload profile picture:</label>
                <input class="form-control-file" type="file" name="media" id="media" />
              </div>
            </div>
          </div>
          <button type="submit" class="btn btn-primary btn-block">Submit</button>
        </form>
      </div>
    </div>

    <script>
      const scriptURL = 'https://script.google.com/macros/s/AKfycbzk0-6-R1JUj4LQwq_BU3F_3RFCI5o90PaC4YxzgJfVGwO9tU8kbIjUtxMp8fsm8u1G/exec';
      const form = document.forms['submit-to-google-sheet'];
      const fileInput = document.getElementById('media');

      document.getElementById('jenjang').addEventListener('change', function () {
        const kelasSelect = document.getElementById('kelas');
        kelasSelect.innerHTML = '';
        kelasSelect.disabled = false;

        let options = [];
        if (this.value === 'SD') {
          options = [1, 2, 3, 4, 5, 6];
        } else if (this.value === 'SMP') {
          options = [7, 8, 9];
        } else if (this.value === 'SMA') {
          options = [10, 11, 12];
        }

        options.forEach(function (option) {
          const opt = document.createElement('option');
          opt.value = option;
          opt.innerHTML = option;
          kelasSelect.appendChild(opt);
        });
      });

      form.addEventListener('submit', async (e) => {
        e.preventDefault();
        const formData = new FormData(form);

        const fileInput = document.getElementById('media');
        if (fileInput.files.length > 0) {
          const file = fileInput.files[0];
          const reader = new FileReader();

          if (file.size > 1024 * 1024 * 2) {
            swal('Error', 'File size should be less than 2MB.', 'error');
            return;
          }

          reader.onload = async function () {
            formData.append('media', reader.result.split(',')[1]);
            await submitForm(formData);
          };

          reader.readAsDataURL(file);
        } else {
          await submitForm(formData);
        }
      });

      async function submitForm(formData) {
        const submitButton = document.querySelector("button[type='submit']");
        submitButton.disabled = true;
        submitButton.innerText = 'Loading...';

        fetch(scriptURL, { method: 'POST', body: formData })
          .then((response) => {
            swal('Done', 'Submitted Successfully.', 'success');
            form.reset();
          })
          .catch((error) => {
            swal('Error', 'Something went wrong. Please try again!', 'error');
          })
          .finally(() => {
            submitButton.disabled = false;
            submitButton.innerText = 'Submit';
          });
      }
    </script>
  </body>
</html>
