# DAFTAR-HADIR
DAFTAR HADIR
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daftar Hadir & Kontrol Belajar - Kelas XI-B SMAN 1 Sabu Timur</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen p-4 sm:p-6">
    <div class="max-w-7xl mx-auto bg-white shadow-lg rounded-2xl p-6 sm:p-8">
        
        <!-- Header -->
        <div class="text-center border-b pb-6 mb-6">
            <h1 class="text-2xl sm:text-3xl font-bold text-blue-900">SMA NEGERI 1 SABU TIMUR</h1>
            <h2 class="text-xl font-semibold text-slate-700 mt-1">Daftar Hadir & Kontrol Belajar Harian - Kelas XI-B</h2>
            <p class="text-sm text-slate-500 mt-2">Pilih Hari, Tanggal, dan kelola catatan kehadiran serta kontrol belajar siswa.</p>
        </div>

        <!-- Filter Hari & Tanggal -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 bg-blue-50 p-4 rounded-xl mb-6">
            <div>
                <label class="block text-sm font-semibold text-blue-900 mb-1">Pilih Hari:</label>
                <select id="selectHari" class="w-full p-2.5 border rounded-lg bg-white focus:ring-2 focus:ring-blue-500">
                    <option value="Senin">Senin</option>
                    <option value="Selasa">Selasa</option>
                    <option value="Rabu">Rabu</option>
                    <option value="Kamis">Kamis</option>
                    <option value="Jumat">Jumat</option>
                    <option value="Sabtu">Sabtu</option>
                </select>
            </div>
            <div>
                <label class="block text-sm font-semibold text-blue-900 mb-1">Pilih Tanggal:</label>
                <input type="date" id="selectTanggal" class="w-full p-2.5 border rounded-lg bg-white focus:ring-2 focus:ring-blue-500">
            </div>
        </div>

        <!-- Tombol Aksi Utama -->
        <div class="flex flex-wrap gap-3 mb-6">
            <button onclick="simpanData()" class="bg-green-600 hover:bg-green-700 text-white font-medium px-5 py-2.5 rounded-lg shadow transition flex items-center gap-2">
                💾 Simpan Data
            </button>
            <button onclick="downloadCSV()" class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-5 py-2.5 rounded-lg shadow transition flex items-center gap-2">
                📥 Download CSV
            </button>
            <button onclick="resetDataHarian()" class="bg-amber-600 hover:bg-amber-700 text-white font-medium px-5 py-2.5 rounded-lg shadow transition flex items-center gap-2">
                🗑️ Hapus / Reset Hari Ini
            </button>
        </div>

        <!-- Tabel Data Siswa -->
        <div class="overflow-x-auto border rounded-xl shadow-sm">
            <table class="w-full text-left border-collapse">
                <thead class="bg-blue-900 text-white text-sm uppercase">
                    <tr>
                        <th class="p-3 text-center w-12">No</th>
                        <th class="p-3">Nama Siswa</th>
                        <th class="p-3 text-center w-36">Kehadiran</th>
                        <th class="p-3">Kontrol Belajar Harian & Catatan</th>
                        <th class="p-3 text-center w-28">Aksi</th>
                    </tr>
                </thead>
                <tbody id="tabelSiswa" class="divide-y divide-slate-200 text-sm">
                    <!-- Data ditaruh secara dinamis via JavaScript -->
                </tbody>
            </table>
        </div>
    </div>

    <!-- Script Aplikasi -->
    <script>
        const daftarSiswa = [
            "ALDYON JHBRIL BALE", "ANASTASIA MAHA ADILLAH", "BIMA CHRISTIAN PUTRA YUSPITA LAY", 
            "CLAUDIA GRACE BATUKH", "DAMARIS NARA DILA", "DANIEL PARA", "DHEGANT SWIYAN DJITA", 
            "DOMINGGUS MANU TARA", "DONI GA", "IRENE KRISTIANI PA", "JESIKA RADJA GERE", 
            "JUANTRISTAN KADJA DILA", "LISA SUSANTY NGEDE", "MATILDA NITA MIRA", "MELDASANTRI BAKI DJU", 
            "NGE MUKA", "PUTRA ANGGUN PRATAMA WOLO", "PUTRI ELISHABET LUDJI LOBO", "RIA LIZHA IVANA NGURU", 
            "RIRIN ELFARINA LAOEPADA", "RISKI ADITIA DJAMI TIA", "ROGER BAU BANI", "SANNY CRISTIN PATRYSIA HAE", 
            "SEPRIADI SAPUTRA LUDJI", "SHIDEN TODE", "VITRY ANISA LOBO", "WANDES SETIAWAN KORE LIHU", 
            "WEHELMINA RIWU WIE", "WENI LILO", "WESLY ZADRAK ARIFIN MINA", "YORITA ROHI MUHU"
        ];

        // Set default tanggal hari ini
        document.getElementById('selectTanggal').valueAsDate = new Date();

        function getStorageKey() {
            const hari = document.getElementById('selectHari').value;
            const tanggal = document.getElementById('selectTanggal').value;
            return `sabu_timur_xi_b_${hari}_${tanggal}`;
        }

        function muatTabel() {
            const tbody = document.getElementById('tabelSiswa');
            tbody.innerHTML = '';
            
            const savedData = JSON.parse(localStorage.getItem(getStorageKey())) || {};

            daftarSiswa.forEach((nama, index) => {
                const dataSiswa = savedData[nama] || { status: 'Hadir', catatan: '' };
                
                const tr = document.createElement('tr');
                tr.className = index % 2 === 0 ? 'bg-white hover:bg-slate-50' : 'bg-slate-50/50 hover:bg-slate-50';
                
                tr.innerHTML = `
                    <td class="p-3 text-center font-medium">${index + 1}</td>
                    <td class="p-3 font-semibold text-slate-700">${nama}</td>
                    <td class="p-3 text-center">
                        <select id="status_${index}" class="p-1.5 border rounded bg-white text-xs font-medium">
                            <option value="Hadir" ${dataSiswa.status === 'Hadir' ? 'selected' : ''}>Hadir</option>
                            <option value="Sakit" ${dataSiswa.status === 'Sakit' ? 'selected' : ''}>Sakit</option>
                            <option value="Izin" ${dataSiswa.status === 'Izin' ? 'selected' : ''}>Izin</option>
                            <option value="Alpa" ${dataSiswa.status === 'Alpa' ? 'selected' : ''}>Alpa</option>
                        </select>
                    </td>
                    <td class="p-3">
                        <input type="text" id="catatan_${index}" value="${dataSiswa.catatan}" placeholder="Tulis kontrol belajar/tugas..." class="w-full p-1.5 border rounded text-xs bg-white">
                    </td>
                    <td class="p-3 text-center">
                        <div class="flex items-center justify-center gap-1">
                            <button onclick="editSiswa('${nama}', ${index})" class="bg-blue-500 text-white px-2 py-1 rounded text-xs hover:bg-blue-600 transition" title="Simpan Baris Ini">Edit</button>
                            <button onclick="hapusBaris(${index})" class="bg-red-500 text-white px-2 py-1 rounded text-xs hover:bg-red-600 transition" title="Kosongkan">Hapus</button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function simpanData() {
            const savedData = {};
            daftarSiswa.forEach((nama, index) => {
                savedData[nama] = {
                    status: document.getElementById(`status_${index}`).value,
                    catatan: document.getElementById(`catatan_${index}`).value
                };
            });
            localStorage.setItem(getStorageKey(), JSON.stringify(savedData));
            alert('Data kehadiran dan kontrol belajar berhasil disimpan!');
        }

        function editSiswa(nama, index) {
            let savedData = JSON.parse(localStorage.getItem(getStorageKey())) || {};
            savedData[nama] = {
                status: document.getElementById(`status_${index}`).value,
                catatan: document.getElementById(`catatan_${index}`).value
            };
            localStorage.setItem(getStorageKey(), JSON.stringify(savedData));
            alert(`Perubahan untuk ${nama} berhasil disimpan.`);
        }

        function hapusBaris(index) {
            document.getElementById(`status_${index}`).value = 'Hadir';
            document.getElementById(`catatan_${index}`).value = '';
            simpanData();
        }

        function resetDataHarian() {
            if (confirm('Apakah Anda yakin ingin mereset seluruh data absensi untuk hari dan tanggal ini?')) {
                localStorage.removeItem(getStorageKey());
                muatTabel();
                alert('Data berhasil direset.');
            }
        }

        function downloadCSV() {
            const hari = document.getElementById('selectHari').value;
            const tanggal = document.getElementById('selectTanggal').value;
            const savedData = JSON.parse(localStorage.getItem(getStorageKey())) || {};

            let csvContent = "data:text/csv;charset=utf-8,No,Nama Siswa,Kehadiran,Kontrol Belajar\n";
            
            daftarSiswa.forEach((nama, index) => {
                const dataSiswa = savedData[nama] || { status: 'Hadir', catatan: '' };
                const row = [index + 1, `"${nama}"`, dataSiswa.status, `"${dataSiswa.catatan}"`];
                csvContent += row.join(",") + "\n";
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Absensi_XI-B_${hari}_${tanggal}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        // Event listener saat pilihan hari atau tanggal diubah
        document.getElementById('selectHari').addEventListener('change', muatTabel);
        document.getElementById('selectTanggal').addEventListener('change', muatTabel);

        // Muat tabel saat halaman pertama kali dibuka
        window.onload = muatTabel;
    </script>
</body>
</html>
