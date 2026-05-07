# Event Ticketing & Booking System
**EF234402 – Konstruksi Perangkat Lunak**  
Institut Teknologi Sepuluh Nopember

---

## Tech Stack

- Language: C# (.NET 8)
- Architecture: Clean Architecture + Domain-Driven Design (DDD)

---

## Initial Business Rules

Business rules berikut diturunkan dari user stories dan acceptance criteria.

### Event

| # | Rule |
|---|------|
| BR-E01 | Event tidak bisa dibuat jika `endDate` lebih awal dari `startDate` |
| BR-E02 | Event tidak bisa dibuat jika `maxCapacity` kurang dari atau sama dengan 0 |
| BR-E03 | Event yang baru dibuat harus berstatus `Draft` |
| BR-E04 | Event hanya bisa dipublish jika memiliki minimal 1 ticket category yang aktif |
| BR-E05 | Event hanya bisa dipublish jika total quota ticket tidak melebihi `maxCapacity` |
| BR-E06 | Event berstatus `Cancelled` tidak bisa dipublish |
| BR-E07 | Event berstatus `Completed` tidak bisa di-cancel |
| BR-E08 | Saat event di-cancel, semua paid booking harus ditandai butuh refund |

### Ticket Category

| # | Rule |
|---|------|
| BR-TC01 | Harga ticket tidak boleh negatif |
| BR-TC02 | Quota ticket harus lebih dari 0 |
| BR-TC03 | Periode penjualan harus berakhir sebelum atau tepat saat `startDate` event |
| BR-TC04 | Total quota semua ticket category tidak boleh melebihi `maxCapacity` event |
| BR-TC05 | Ticket category yang sudah dinonaktifkan tidak bisa dibeli |
| BR-TC06 | Ticket category yang sudah dinonaktifkan tapi ada bookingnya tetap disimpan |

### Booking

| # | Rule |
|---|------|
| BR-B01 | Booking hanya bisa dibuat untuk event berstatus `Published` |
| BR-B02 | Booking hanya bisa dibuat untuk ticket category yang aktif |
| BR-B03 | Booking hanya bisa dibuat dalam periode penjualan ticket |
| BR-B04 | Quantity ticket harus lebih dari 0 |
| BR-B05 | Quantity tidak boleh melebihi sisa quota ticket |
| BR-B06 | Customer tidak boleh punya lebih dari 1 active booking untuk event yang sama |
| BR-B07 | Booking yang baru dibuat harus berstatus `PendingPayment` |
| BR-B08 | Booking harus punya payment deadline (contoh: 15 menit setelah dibuat) |
| BR-B09 | Booking hanya bisa dibayar jika statusnya `PendingPayment` |
| BR-B10 | Booking tidak bisa dibayar jika payment deadline sudah lewat |
| BR-B11 | Jumlah pembayaran harus sama persis dengan total harga booking |
| BR-B12 | Setelah dibayar, sistem menerbitkan ticket dengan kode unik |
| BR-B13 | Booking `Paid` tidak bisa di-expire |
| BR-B14 | Saat booking expire, quota ticket yang direservasi dilepas kembali |

### Ticket & Check-in

| # | Rule |
|---|------|
| BR-T01 | Hanya ticket berstatus `Active` yang bisa di-check-in |
| BR-T02 | Ticket yang sudah di-check-in tidak bisa digunakan lagi |
| BR-T03 | Check-in hanya bisa dilakukan untuk event yang sesuai dengan ticket |
| BR-T04 | Check-in hanya bisa dilakukan pada hari event atau dalam jendela waktu yang diizinkan |

### Refund

| # | Rule |
|---|------|
| BR-R01 | Refund hanya bisa diminta untuk booking berstatus `Paid` |
| BR-R02 | Refund tidak bisa diminta jika ada ticket dari booking tersebut yang sudah check-in |
| BR-R03 | Refund hanya bisa diminta sebelum deadline refund |
| BR-R04 | Jika event di-cancel, refund otomatis diizinkan |
| BR-R05 | Refund hanya bisa di-approve jika statusnya `Requested` |
| BR-R06 | Refund hanya bisa di-reject jika statusnya `Requested` |
| BR-R07 | Penolakan refund harus disertai alasan |
| BR-R08 | Refund hanya bisa di-payout jika statusnya `Approved` |
| BR-R09 | Refund yang sudah `PaidOut` tidak bisa diubah lagi |

---



### Domain Events (yang akan di-raise)

| Domain Event | Di-raise saat |
|---|---|
| `EventCreated` | Event berhasil dibuat |
| `EventPublished` | Event dipublish |
| `EventCancelled` | Event di-cancel |
| `TicketCategoryCreated` | Ticket category ditambahkan |
| `TicketCategoryDisabled` | Ticket category dinonaktifkan |
| `TicketReserved` | Booking berhasil dibuat |
| `BookingPaid` | Booking berhasil dibayar |
| `BookingExpired` | Booking expire karena lewat deadline |
| `TicketCheckedIn` | Ticket berhasil di-check-in |
| `RefundRequested` | Customer mengajukan refund |
| `RefundApproved` | Organizer menyetujui refund |
| `RefundRejected` | Organizer menolak refund |
| `RefundPaidOut` | Admin menandai refund sudah dibayar |

---

## Ubiquitous Language Glossary

| Term | Makna |
|---|---|
| **Event** | Aktivitas yang diselenggarakan oleh Event Organizer dan dihadiri oleh Customer |
| **Event Organizer** | User yang membuat dan mengelola event |
| **Customer** | User yang memesan dan membeli tiket |
| **Gate Officer** | User yang memvalidasi tiket saat check-in |
| **System Admin** | User yang memproses refund payout |
| **Ticket Category** | Jenis tiket dalam suatu event, contoh: Regular, VIP, Early Bird |
| **Quota** | Jumlah maksimal tiket yang tersedia dalam satu ticket category |
| **Booking** | Reservasi sementara yang dibuat sebelum pembayaran diselesaikan |
| **Pending Payment** | Status booking: pembayaran belum diselesaikan |
| **Paid** | Status booking: pembayaran sudah selesai |
| **Expired** | Status booking: batas waktu pembayaran sudah terlewat |
| **Refunded** | Status booking: sudah direfund |
| **Ticket** | Bukti kehadiran yang diterbitkan setelah booking dibayar |
| **Ticket Code** | Kode unik yang digunakan untuk mengidentifikasi dan memvalidasi tiket |
| **Check-in** | Proses validasi tiket ketika peserta memasuki venue event |
| **Refund** | Proses pengembalian uang kepada customer |
| **Money** | Value object yang merepresentasikan jumlah uang dan mata uang |
| **Sales Period** | Periode waktu di mana ticket category dapat dibeli |
| **Payment Deadline** | Batas waktu untuk menyelesaikan pembayaran setelah booking dibuat |
| **Max Capacity** | Jumlah maksimal peserta yang dapat hadir dalam suatu event |
| **Draft** | Status event: belum dipublish, belum bisa dilihat customer |
| **Published** | Status event: sudah dipublish, bisa dilihat dan dibooking customer |
| **Cancelled** | Status event: dibatalkan, penjualan tiket dihentikan |
| **Completed** | Status event: event sudah selesai diselenggarakan |
| **Active** | Status ticket: valid dan belum digunakan |
| **Checked In** | Status ticket: sudah divalidasi saat masuk venue |
| **Aggregate** | Kumpulan entity yang diperlakukan sebagai satu unit dalam perubahan data |
| **Domain Event** | Sesuatu yang terjadi di domain dan penting untuk dicatat |
| **Value Object** | Objek yang nilainya ditentukan oleh atributnya, bukan identitasnya |
