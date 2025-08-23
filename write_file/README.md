
```golang
package main

import (
    "bytes"
    "fmt"
    "io"
    "os"
    "unicode/utf8"
)

func main() {
    // Mendeklarasikan slice dari byte slices dengan tipe [][]byte
    var bytesSlices [][]byte = [][]byte{
        {0xe3, 0x81, 0x94}, // ご
        {0xe3, 0x81, 0xaf}, // は
        {0xe3, 0x81, 0xbf}, // み
        {0xe3, 0x81, 0x9a}, // ず
        {0xe3, 0x81, 0xb2}, // ひ
        {0xe3, 0x81, 0xa8}, // と
        {0xe3, 0x81, 0x95}, // さ
        {0xe3, 0x81, 0x9b}, // せ
        {0xe3, 0x81, 0xa7}, // で
        {0xe3, 0x81, 0x8c}, // が
        {0xe3, 0x81, 0x91}, // け
        {0xe3, 0x81, 0x93}, // 
        {0xe3, 0x81, 0xa9}, // ど
        {0xe3, 0x81, 0x98}, // 
    }

    // Mendeklarasikan slice dari string dengan tipe []string
    var labels []string = []string{
        " (go) 1", " (ha) 2", " (mi) 3", " (zu) 4", " (hi) 5",
        " (to) 6", " (sa) 7", " (se) 8", " (de) 9", " (ga) 10", " (ke) 11", " (ko) 12", " (do) 13", " (ji) 14",
    }

    // Mendeklarasikan variabel file bertipe *os.File
    var file *os.File
    var err error

    // Membuka file output.txt dengan tipe *os.File
    file, err = os.Create("output.txt")
    if err != nil {
        // Error handling dengan tipe error
        fmt.Println("Gagal membuat file:", err)
        return
    }
    // Pastikan file tertutup setelah selesai
    defer file.Close()

    // Mendeklarasikan variabel dalam loop
    var (
        i          int            // indeks loop bertipe int
        byteSlice  []byte         // slice byte bertipe []byte
        buffer     bytes.Buffer   // buffer untuk menulis data ke file
        s          string         // string hasil konversi byteSlice
        outputLine string         // string yang akan ditulis ke buffer
    )

    // Loop melalui setiap byte slice dengan indeks dan nilainya
    for i, byteSlice = range bytesSlices {
        // Periksa validitas UTF-8 dari byteSlice
        if utf8.Valid(byteSlice) {
            s = string(byteSlice) // konversi byteSlice ke string
            outputLine = fmt.Sprintf("%d %s: %s\n", i+1, labels[i], s)
        } else {
            outputLine = fmt.Sprintf("%d %s: Invalid UTF-8 sequence\n", i+1, labels[i])
        }
        // Tulis hasil ke buffer
        fmt.Fprintf(&buffer, "%s", outputLine)
        // Tulis isi buffer ke file menggunakan io.Copy
        io.Copy(file, &buffer)
        // Reset buffer setelah menulis
        buffer.Reset()
    }

    fmt.Println("Output berhasil disimpan ke output.txt")
}
```

```golang
package main

import (
    "bytes"
    "fmt"
    "io"
    "os"
    "unicode/utf8"
)

func main() {
    // Mendeklarasikan slice dari byte slices dengan tipe [][]byte
    var bytesSlices [][]byte = [][]byte{
        {0xe3, 0x81, 0x94}, // ご
        {0xe3, 0x81, 0xaf}, // は
        {0xe3, 0x81, 0xbf}, // み
        {0xe3, 0x81, 0x9a}, // ず
        {0xe3, 0x81, 0xb2}, // ひ
        {0xe3, 0x81, 0xa8}, // と
        {0xe3, 0x81, 0x95}, // さ
        {0xe3, 0x81, 0x9b}, // せ
        {0xe3, 0x81, 0xa7}, // で
        {0xe3, 0x81, 0x8c}, // が
        {0xe3, 0x81, 0x91}, // け
    }

    // Mendeklarasikan slice dari string dengan tipe []string
    var labels []string = []string{
        " (go)", " (ha)", " (mi)", " (zu)", " (hi)",
        " (to)", " (sa)", " (se)", " (de)", " (ga)", " (ke)",
    }

    // Membuka file output.txt dengan tipe *os.File
    file, err := os.Create("output.txt")
    if err != nil {
        fmt.Println("Gagal membuat file:", err)
        os.Exit(1)
    }
    // Pastikan file tertutup setelah selesai
    defer file.Close()

    // Loop melalui setiap byte slice dengan indeks dan nilainya
    for i, byteSlice := range bytesSlices {
        var outputLine string
        if utf8.Valid(byteSlice) {
            s := string(byteSlice)
            outputLine = fmt.Sprintf("%d %s: %s\n", i+1, labels[i], s)
        } else {
            outputLine = fmt.Sprintf("%d %s: Invalid UTF-8 sequence\n", i+1, labels[i])
        }
        // Tulis hasil ke file langsung tanpa buffer
        _, err := io.WriteString(file, outputLine)
        if err != nil {
            fmt.Println("Gagal menulis ke file:", err)
            os.Exit(1)
        }
    }

    fmt.Println("Output berhasil disimpan ke output.txt")
}
```
