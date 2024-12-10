##  TSP (Traveling Salesman Problem)

### Install dependencies
`import numpy as np
import matplotlib.pyplot as plt
`
#### Tạo dữ liệu thành phố (tọa độ ngẫu nhiên)
```
def tao_thanh_pho(so_thanh_pho, kich_thuoc=100):
    return np.random.rand(so_thanh_pho, 2) * kich_thuoc```
```

#### Tính khoảng cách giữa hai thành phố
```
def khoang_cach_euclid(thanh_pho_1, thanh_pho_2):
    return np.sqrt(np.sum((thanh_pho_1 - thanh_pho_2) ** 2))
```
#### Tính tổng độ dài quãng đường của một lộ trình
```
def tinh_quang_duong_lo_trinh(lo_trinh, danh_sach_thanh_pho):
    quang_duong = 0
    for i in range(len(lo_trinh)):
        quang_duong += khoang_cach_euclid(danh_sach_thanh_pho[lo_trinh[i - 1]], danh_sach_thanh_pho[lo_trinh[i]])
    return quang_duong
```
#### Tạo tổ ngẫu nhiên (tổ của chim cu cu)
```
def tao_to_ngau_nhien(so_to, so_thanh_pho):
    return [np.random.permutation(so_thanh_pho) for _ in range(so_to)]
```

#### Thay thế tổ xấu bằng tổ mới
```
def thay_to_xau(to, do_thich_nghi, so_to_thay):
    vi_tri_xau = np.argsort(do_thich_nghi)[-so_to_thay:]
    for idx in vi_tri_xau:
        to[idx] = np.random.permutation(len(to[0]))
    return to
```
#### Tạo tổ mới (bằng cách hoán vị)
```
def tao_giai_phap_moi(to):
    to_moi = to.copy()
    i, j = np.random.choice(len(to), 2, replace=False)
    to_moi[i], to_moi[j] = to_moi[j], to_moi[i]
    return to_moi
```
#### Thuật toán Cuckoo Search
```
def tim_duong_cu_cu(so_thanh_pho, so_to, so_the_he, pa=0.25):
    danh_sach_thanh_pho = tao_thanh_pho(so_thanh_pho)  # Tạo danh sách thành phố
    to = tao_to_ngau_nhien(so_to, so_thanh_pho)  # Tạo tổ ban đầu
    quang_duong_tot_nhat = float("inf")
    lo_trinh_tot_nhat = None

    for the_he in range(so_the_he):
        do_thich_nghi = np.array([tinh_quang_duong_lo_trinh(to[i], danh_sach_thanh_pho) for i in range(so_to)])

        # Cập nhật tổ tốt nhất
        vi_tri_tot_nhat = np.argmin(do_thich_nghi)
        if do_thich_nghi[vi_tri_tot_nhat] < quang_duong_tot_nhat:
            quang_duong_tot_nhat = do_thich_nghi[vi_tri_tot_nhat]
            lo_trinh_tot_nhat = to[vi_tri_tot_nhat]

        # Tạo tổ mới
        for i in range(so_to):
            giai_phap_moi = tao_giai_phap_moi(to[i])
            quang_duong_moi = tinh_quang_duong_lo_trinh(giai_phap_moi, danh_sach_thanh_pho)
            if quang_duong_moi < do_thich_nghi[i]:
                to[i] = giai_phap_moi

        # Thay tổ xấu
        to = thay_to_xau(to, do_thich_nghi, int(pa * so_to))

        # In kết quả mỗi thế hệ
        print(f"Thế hệ {the_he + 1}: Độ dài tốt nhất = {quang_duong_tot_nhat}")

    return lo_trinh_tot_nhat, quang_duong_tot_nhat, danh_sach_thanh_pho
```
#### Chạy thuật toán
```
so_thanh_pho = 10  # Số thành phố
so_to = 20  # Số tổ
so_the_he = 100  # Số thế hệ

lo_trinh_tot_nhat, quang_duong_tot_nhat, danh_sach_thanh_pho = tim_duong_cu_cu(so_thanh_pho, so_to, so_the_he)

#### Hiển thị kết quả
print("\nLộ trình tốt nhất:", lo_trinh_tot_nhat)
print("Độ dài tốt nhất:", quang_duong_tot_nhat)
```
#### Vẽ đồ thị
```
def ve_lo_trinh(danh_sach_thanh_pho, lo_trinh):
    cac_thanh_pho = danh_sach_thanh_pho[lo_trinh]
    cac_thanh_pho = np.vstack((cac_thanh_pho, cac_thanh_pho[0]))  # Quay lại thành phố đầu
    plt.plot(cac_thanh_pho[:, 0], cac_thanh_pho[:, 1], 'b-o')
    plt.scatter(danh_sach_thanh_pho[:, 0], danh_sach_thanh_pho[:, 1], c='red', marker='x')
    plt.title(f"Lộ trình ngắn nhất (Độ dài = {quang_duong_tot_nhat:.2f})")
    plt.xlabel("Tọa độ X")
    plt.ylabel("Tọa độ Y")
    plt.show()

ve_lo_trinh(danh_sach_thanh_pho, lo_trinh_tot_nhat)
```
