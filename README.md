# aborakanon_rcraps
* **Timeout()**: Error disebabkan oleh operasi yang melebihi batas waktu yang ditentukan.
* **Temporary()**: Error bersifat sementara dan operasi bisa diulang kembali setelah beberapa saat.

```golang
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"time"
)

func main() {
	// 1. Buat koneksi TCP ke server
	conn, err := net.DialTimeout("tcp", "httpbin.org:80", 5*time.Second)
	if err != nil {
		fmt.Println("Error: Gagal membuat koneksi:", err)
		os.Exit(1)
	}
	defer conn.Close()

	// 2. Kirim HTTP GET request
	request := "GET /get HTTP/1.1\r\nHost: httpbin.org\r\nUser-Agent: Go-Client\r\nConnection: close\r\n\r\n"
	_, err = conn.Write([]byte(request))
	if err != nil {
		fmt.Println("Error: Gagal mengirim request:", err)
		os.Exit(1)
	}

	// 3. Baca response dari server
	reader := bufio.NewReader(conn)
	response, err := reader.ReadString('\n')
	if err != nil {
		fmt.Println("Error: Gagal membaca response:", err)
		os.Exit(1)
	}

	// 4. Tampilkan response
	fmt.Println("Response dari server:")
	fmt.Println(response)

	// 5. Exit dengan kode 0 (sukses)
	os.Exit(0)
}

```

## Revisi
conn.SetReadDeadline untuk mengatur timeout saat membaca data dari server.
```golang
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"time"
)

func main() {
	// 1. Buat koneksi TCP ke server dengan timeout 5 detik
	conn, err := net.DialTimeout("tcp", "httpbin.org:80", 5*time.Second)
	if err != nil {
		fmt.Println("Error: Gagal membuat koneksi:", err)
		os.Exit(1)
	}
	defer conn.Close()

	// 2. Kirim HTTP GET request
	request := "GET /get HTTP/1.1\r\nHost: httpbin.org\r\nUser-Agent: Go-Client\r\nConnection: close\r\n\r\n"
	_, err = conn.Write([]byte(request))
	if err != nil {
		fmt.Println("Error: Gagal mengirim request:", err)
		os.Exit(1)
	}

	// 3. Set timeout untuk membaca response (5 detik)
	conn.SetReadDeadline(time.Now().Add(5 * time.Second))

	// 4. Baca response dari server
	reader := bufio.NewReader(conn)
	response, err := reader.ReadString('\n')
	if err != nil {
		// Cek apakah error disebabkan oleh timeout
		if netErr, ok := err.(net.Error); ok && netErr.Timeout() {
			fmt.Println("Error: Timeout saat membaca response dari server")
		} else {
			fmt.Println("Error: Gagal membaca response:", err)
		}
		os.Exit(1)
	}

	// 5. Tampilkan response
	fmt.Println("Response dari server:")
	fmt.Println(response)

	// 6. Exit dengan kode 0 (sukses)
	os.Exit(0)
}

```

```python
import socket

def main():
    try:
        # 1. Buat koneksi TCP ke server dengan timeout 5 detik
        conn = socket.create_connection(("httpbin.org", 80), timeout=5)
        conn.settimeout(5)  # Set timeout untuk membaca response

        # 2. Kirim HTTP GET request
        request = "GET /get HTTP/1.1\r\nHost: httpbin.org\r\nUser-Agent: Python-Client\r\nConnection: close\r\n\r\n"
        conn.sendall(request.encode())

        # 3. Baca response dari server
        response = conn.recv(1024)  # Baca hingga 1024 byte
        if not response:
            print("Error: Tidak ada response dari server")
            return

        # 4. Tampilkan response
        print("Response dari server:")
        print(response.decode())

    except socket.timeout:
        # Handle timeout saat membaca response
        print("Error: Timeout saat membaca response dari server")
    except socket.error as err:
        # Handle error lainnya
        print(f"Error: Gagal melakukan operasi socket: {err}")
    finally:
        # 5. Tutup koneksi
        if 'conn' in locals():
            conn.close()

if __name__ == "__main__":
    main()

```

```python
import socket

def main():
    try:
        # 1. Buat koneksi TCP ke server dengan timeout 5 detik
        conn = socket.create_connection(("httpbin.org", 80), timeout=5)
        conn.settimeout(5)  # Set timeout untuk membaca response

        # 2. Kirim HTTP GET request
        request = "GET /get HTTP/1.1\r\nHost: httpbin.org\r\nUser-Agent: Python-Client\r\nConnection: close\r\n\r\n"
        conn.sendall(request.encode())

        # 3. Baca seluruh response dari server
        response = b""  # Inisialisasi variabel untuk menyimpan response
        while True:
            data = conn.recv(1024)  # Baca 1024 byte setiap iterasi
            if not data:
                break  # Jika tidak ada data lagi, keluar dari loop
            response += data  # Tambahkan data ke response

        # 4. Tampilkan response
        print("Response dari server:")
        print(response.decode())

    except socket.timeout:
        # Handle timeout saat membaca response
        print("Error: Timeout saat membaca response dari server")
    except socket.error as err:
        # Handle error lainnya
        print(f"Error: Gagal melakukan operasi socket: {err}")
    finally:
        # 5. Tutup koneksi
        if 'conn' in locals():
            conn.close()

if __name__ == "__main__":
    main()

```


