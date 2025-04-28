# NIRRT*

Tất cả mã được phát triển và kiểm tra trên Ubuntu 20.04 với CUDA 12.0, conda 23.11.0, Python 3.9.0 và PyTorch 2.0.1.  
Chúng tôi cũng cung cấp các phiên bản triển khai RRT*, Informed RRT* và Neural RRT* làm cơ sở so sánh.

## Trích dẫn
Nếu bạn thấy kho lưu trữ này hữu ích, vui lòng trích dẫn
```
@inproceedings{huang2024neural,
  title={Neural Informed RRT*: Learning-based Path Planning with Point Cloud State Representations under Admissible Ellipsoidal Constraints},
  author={Huang, Zhe and Chen, Hongyu and Pohovey, John and Driggs-Campbell, Katherine},
  booktitle={2024 IEEE International Conference on Robotics and Automation (ICRA)},
  pages={8742--8748},
  year={2024},
  organization={IEEE}
}
```

## Cài đặt
Chạy:
```
conda env create -f environment.yml
```
(được khuyến nghị). Hoặc chạy:
```
conda create -n pngenv python=3.9.0
conda activate pngenv
pip install numpy==1.25.0
pip install pyyaml
pip install matplotlib
pip install opencv-python
pip install torch==2.0.1
pip install torchvision==0.15.2
pip install open3d==0.17.0
```

## Kiểm tra nhanh

### Dữ liệu cho ICRA 2024
Tải [nirrt_star-icra24-data.zip](https://drive.google.com/file/d/1omIxfoASMzWBUcdXS7-9WiJbc2xTikf2/view?usp=drive_link) và di chuyển tệp zip vào thư mục gốc của kho lưu trữ này.  
Sau đó chạy:
```
cd nirrt_star/
unzip nirrt_star-icra24-data.zip
```

### Trọng số mô hình cho ICRA 2024
Tải [nirrt_star-icra24-model-weights.zip](https://drive.google.com/file/d/1jQ7yrxceSq1aHZjdDQYq2gB2HOHRPvCg/view?usp=drive_link) và di chuyển tệp zip vào thư mục gốc của kho lưu trữ này.  
Sau đó chạy:
```
cd nirrt_star/
unzip nirrt_star-icra24-model-weights.zip
```

### Đánh giá cho ICRA 2024
Tải [nirrt_star-icra24-evaluation.zip](https://drive.google.com/file/d/1CZyPvj3K53vaXxOVTOpZCIU33zIg9dAS/view?usp=drive_link) và di chuyển tệp zip vào thư mục gốc của kho lưu trữ này.  
Sau đó chạy:
```
cd nirrt_star/
unzip nirrt_star-icra24-evaluation.zip
```

### Trực quan hóa mẫu Thế giới Ngẫu nhiên 2D
- Ví dụ: Để trực quan hóa mẫu kiểm tra Thế giới Ngẫu nhiên 2D với token `100_2`, chạy:
```
conda activate pngenv
python visualize_data_samples_2d.py --visual_example_token 100_2
```
Kiểm tra hình ảnh trong `visualization/img_with_labels_2d/`.

- Để trực quan hóa tất cả các mẫu kiểm tra 2D:
```
conda activate pngenv
python visualize_data_samples_2d.py
```

### Demo Lập kế hoạch
Chạy lần lượt các lệnh sau:
```
conda activate pngenv
python demo_planning_2d.py -p nirrt_star -n pointnet2 -c bfs --problem {2D_problem_type} --iter_max 500
python demo_planning_2d.py -p nrrt_star -n unet --problem {2D_problem_type} --iter_max 500
python demo_planning_2d.py -p nrrt_star -n pointnet2 --problem {2D_problem_type} --iter_max 500
python demo_planning_2d.py -p irrt_star --problem {2D_problem_type} --iter_max 500
python demo_planning_2d.py -p rrt_star --problem {2D_problem_type} --iter_max 500
```
Trong đó `{2D_problem_type}` có thể là `random_2d`, `block`, hoặc `gap`.

> **Lưu ý:** `unet` không thể sử dụng với `block`, vì `unet` yêu cầu `img_height % 32 == 0 and img_width % 32 == 0`.

Kết quả trực quan hóa sẽ hiển thị trong giao diện GUI và lưu ở `visualization/planning_demo/`.

### Hình ảnh trực quan trong bài báo ICRA 2024
Nếu bạn thực hiện [Phân tích Kết quả](#phân-tích-kết-quả), bạn sẽ tìm thấy hình ảnh sử dụng trong Hình 5 của bài báo NIRRT* tại `visualization/evaluation/`.

## Thu thập Dữ liệu

### Thu thập dữ liệu thế giới ngẫu nhiên 2D
```
conda activate pngenv
python generate_random_world_env_2d.py
python generate_random_world_env_2d_point_cloud.py
```

### Thu thập dữ liệu thế giới ngẫu nhiên 3D
```
conda activate pngenv
python generate_random_world_env_3d_raw.py
python generate_random_world_env_3d_astar_labels.py
python generate_random_world_env_3d_point_cloud.py
```

### Tạo cấu hình môi trường block và gap
```
conda activate pngenv
python generate_block_gap_env_2d.py
```

## Huấn luyện Mô hình

### Huấn luyện và đánh giá PointNet++
```
conda activate pngenv
python train_pointnet_pointnet2.py --random_seed 100 --model pointnet2 --dim 2
python eval_pointnet_pointnet2.py --random_seed 100 --model pointnet2 --dim 2
python train_pointnet_pointnet2.py --random_seed 100 --model pointnet2 --dim 3
python eval_pointnet_pointnet2.py --random_seed 100 --model pointnet2 --dim 3
```
> Nếu bạn muốn huấn luyện PointNet thay vì PointNet++, thay `--model pointnet2` bằng `--model pointnet`.

### Huấn luyện và đánh giá U-Net
```
conda activate pngenv
python train_unet.py
python eval_unet.py
```

## Đánh giá các Phương pháp Lập kế hoạch

### 2D
Chạy:
```
conda activate pngenv
python eval_planning_2d.py -p nirrt_star -n pointnet2 -c bfs --problem {2D_problem_type}
python eval_planning_2d.py -p nirrt_star -n pointnet2 --problem {2D_problem_type}
python eval_planning_2d.py -p nrrt_star -n pointnet2 -c bfs --problem {2D_problem_type}
python eval_planning_2d.py -p nrrt_star -n pointnet2 --problem {2D_problem_type}
python eval_planning_2d.py -p nrrt_star -n unet --problem {2D_problem_type}
python eval_planning_2d.py -p irrt_star --problem {2D_problem_type}
python eval_planning_2d.py -p rrt_star --problem {2D_problem_type}
```
Trong đó `{2D_problem_type}` là `random_2d`, `block`, hoặc `gap`.

> **Lưu ý:** `unet` không thể sử dụng cho `block`.

### Phân tích Kết quả
Chạy:
```
conda activate pngenv
python result_analysis_random_world_2d.py
python result_analysis_random_world_3d.py
python result_analysis_block.py
python result_analysis_gap.py
```
Kết quả được lưu tại `visualization/evaluation/`.
