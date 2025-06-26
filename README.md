# aborakanon_rcraps
**Timeout()**: Error disebabkan oleh operasi yang melebihi batas waktu yang ditentukan.
**Temporary()**: Error bersifat sementara dan operasi bisa diulang kembali setelah beberapa saat.

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



