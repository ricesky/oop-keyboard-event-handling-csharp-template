# Tutorial: Keyboard Event Handling dengan Windows Form di C#

## Tujuan
Mempelajari cara menangani event keyboard pada Windows Form tanpa menggunakan designer. Aplikasi ini akan menampilkan kotak (`Box`) yang bisa bergerak dan berubah warna berdasarkan input keyboard (panah atas/W, bawah/S, kiri/A, dan kanan/D, serta spasi untuk mengubah warna).

## Sub Capaian Pembelajaran
Mahasiswa mampu mengimplementasikan event handler untuk input keyboard pada Windows Form dan mengatur gerakan objek menggunakan prinsip **encapsulation**.

## Lingkungan Pengembangan
1. Platform: .NET 6.0
2. Bahasa: C# 10
3. IDE: Visual Studio 2022

## Cara membuka project menggunakan Visual Studio
1. Clone repositori project `oop-keyboard-event-handling-csharp` ke direktori lokal git Anda.
2. Buka Visual Studio, pilih menu File > Open > Project/Solution > Pilih file *.sln.
3. Tekan tombol Open untuk membuka solusi.
4. Baca tutorial dengan seksama dan buat implementasi kode sesuai dengan petunjuk.

## Struktur Proyek

Proyek ini terdiri dari tiga kelas:
- **Box**: Menangani tampilan dan logika pergerakan `PictureBox`.
- **MainForm**: Form utama yang menangani event keyboard dan mengatur `Box`.
- **Program**: Kelas utama yang menjalankan aplikasi.

## Langkah-langkah

### 0. Membuat Proyek dan Solusi (Opsional)
1. Buka Visual Studio dan buat proyek Console baru dengan nama `KeyboardEventHandling`.
2. Pilih target framework .NET 6.0.
3. Klik `Create`.
4. Pastikan ada referensi **System.Windows.Forms** dan **System.Drawing**.

### 1. Membuat Kelas `Box`

Kelas `Box` akan mengenkapsulasi `PictureBox` sebagai objek yang bisa bergerak dan berubah warna. Kelas ini akan memiliki metode untuk menggerakkan `Box` ke atas, bawah, kiri, dan kanan, serta mengubah warna secara acak.

```csharp
using System;
using System.Drawing;
using System.Windows.Forms;

namespace KeyboardEventHandling
{
    public class Box
    {
        private PictureBox _pictureBox;
        private int _moveSpeed;

        public Box(int x, int y, Color color, Size size, int moveSpeed)
        {
            _pictureBox = new PictureBox
            {
                Size = size,
                BackColor = color,
                Location = new Point(x, y)
            };
            _moveSpeed = moveSpeed;
        }

        public void MoveUp()
        {
            if (_pictureBox.Location.Y > 0)
            {
                _pictureBox.Location = new Point(_pictureBox.Location.X, _pictureBox.Location.Y - _moveSpeed);
            }
        }

        public void MoveDown(int maxHeight)
        {
            if (_pictureBox.Location.Y + _pictureBox.Height < maxHeight)
            {
                _pictureBox.Location = new Point(_pictureBox.Location.X, _pictureBox.Location.Y + _moveSpeed);
            }
        }

        public void MoveLeft()
        {
            if (_pictureBox.Location.X > 0)
            {
                _pictureBox.Location = new Point(_pictureBox.Location.X - _moveSpeed, _pictureBox.Location.Y);
            }
        }

        public void MoveRight(int maxWidth)
        {
            if (_pictureBox.Location.X + _pictureBox.Width < maxWidth)
            {
                _pictureBox.Location = new Point(_pictureBox.Location.X + _moveSpeed, _pictureBox.Location.Y);
            }
        }

        public void ChangeColor()
        {
            Random random = new Random();
            _pictureBox.BackColor = Color.FromArgb(random.Next(256), random.Next(256), random.Next(256));
        }

        public PictureBox GetPictureBox()
        {
            return _pictureBox;
        }
    }
}
```

- **Penjelasan**:
  - `MoveUp`, `MoveDown`, `MoveLeft`, dan `MoveRight`: Menggerakkan `Box` sesuai arah yang ditentukan dengan kecepatan `_moveSpeed`.
  - `ChangeColor`: Mengubah warna `Box` secara acak.
  - `GetPictureBox`: Mengembalikan objek `PictureBox` untuk ditambahkan ke form.

### 2. Membuat Kelas `MainForm`

Kelas `MainForm` adalah form utama yang akan menampung objek `Box` dan menangani event keyboard. Timer digunakan untuk memperbarui posisi `Box` berdasarkan input keyboard.

```csharp
using System;
using System.Drawing;
using System.Windows.Forms;

namespace KeyboardEventHandling
{
    public class MainForm : Form
    {
        private Box _box;
        private int _moveSpeed = 10;
        private bool _goLeft, _goRight, _goUp, _goDown, _changeColor;
        private Size _screenSize;

        public MainForm()
        {
            _screenSize = new Size(400, 400);

            // Mengatur properti dasar form
            this.Text = "Keyboard Event Handling";
            this.Size = _screenSize;
            this.StartPosition = FormStartPosition.CenterScreen;

            // Inisialisasi Box dan menambahkannya ke form
            _box = new Box(175, 175, Color.Blue, new Size(50, 50), _moveSpeed);
            this.Controls.Add(_box.GetPictureBox());

            // Menangani event KeyDown dan KeyUp untuk menangkap input keyboard
            this.KeyDown += new KeyEventHandler(OnKeyDown);
            this.KeyUp += new KeyEventHandler(OnKeyUp);

            // Timer untuk mengontrol gerakan objek
            Timer timer = new Timer();
            timer.Interval = 20;
            timer.Tick += new EventHandler(UpdatePosition);
            timer.Start();
        }

        private void OnKeyDown(object sender, KeyEventArgs e)
        {
            // Menentukan tombol mana yang ditekan dan mengubah nilai boolean sesuai tombol
            if (e.KeyCode == Keys.Left || e.KeyCode == Keys.A) _goLeft = true;
            if (e.KeyCode == Keys.Right || e.KeyCode == Keys.D) _goRight = true;
            if (e.KeyCode == Keys.Up || e.KeyCode == Keys.W) _goUp = true;
            if (e.KeyCode == Keys.Down || e.KeyCode == Keys.S) _goDown = true;
            if (e.KeyCode == Keys.Space) _changeColor = true;
        }

        private void OnKeyUp(object sender, KeyEventArgs e)
        {
            // Mengatur kembali nilai boolean saat tombol dilepaskan
            if (e.KeyCode == Keys.Left || e.KeyCode == Keys.A) _goLeft = false;
            if (e.KeyCode == Keys.Right || e.KeyCode == Keys.D) _goRight = false;
            if (e.KeyCode == Keys.Up || e.KeyCode == Keys.W) _goUp = false;
            if (e.KeyCode == Keys.Down || e.KeyCode == Keys.S) _goDown = false;
            if (e.KeyCode == Keys.Space) _changeColor = false;
        }

        private void UpdatePosition(object sender, EventArgs e)
        {
            // Menggerakkan objek sesuai tombol yang ditekan
            if (_goLeft) _box.MoveLeft();
            if (_goRight) _box.MoveRight(_screenSize.Width);
            if (_goUp) _box.MoveUp();
            if (_goDown) _box.MoveDown(_screenSize.Height);
            if (_changeColor) _box.ChangeColor();
        }
    }
}
```

- **Penjelasan**:
  - `OnKeyDown` dan `OnKeyUp`: Mengatur variabel boolean untuk setiap tombol yang ditekan atau dilepaskan.
  - `UpdatePosition`: Memanggil metode pergerakan di `Box` sesuai arah tombol yang ditekan. Jika tombol spasi ditekan, `Box` akan berubah warna.

### 3. Membuat Kelas `Program`

Kelas `Program` berfungsi sebagai entry point untuk menjalankan aplikasi.

```csharp
using System;
using System.Windows.Forms;

namespace KeyboardEventHandling
{
    internal class Program
    {
        [STAThread]
        static void Main(string[] args)
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            Application.Run(new MainForm());
        }
    }
}
```

---

### 4. Menjalankan Aplikasi

Jalankan aplikasi dengan menekan **F5** atau klik **Start**.

### Uji Program
- **Tekan tombol panah atau WASD**: `Box` akan bergerak sesuai arah yang ditekan.
- **Tekan tombol spasi**: `Box` akan berubah warna secara acak.

Dengan tutorial ini, Anda mempelajari cara menangani event keyboard untuk mengontrol objek di Windows Form dan mengimplementasikan **encapsulation** dengan memisahkan logika `Box` dari `MainForm`.