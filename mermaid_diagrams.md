# Mermaid Diagrams - VeritaShop

## 1. Sơ đồ Kiến trúc Hệ thống

```mermaid
flowchart LR
    subgraph Client["Flutter App"]
        A[UI] --> B[ViewModel] --> C[Repository]
    end
    
    subgraph Server["Node.js Backend"]
        D[Routes] --> E[Controller] --> F[Service]
    end
    
    subgraph DB["Database"]
        G[(MongoDB)]
    end
    
    subgraph Ext["External"]
        H[Cloudinary]
        I[MoMo]
        J[ABSA]
    end
    
    C <-->|REST API| D
    F <--> G
    F <--> H & I & J
```

---

## 2. Kiến trúc STL cho ABSA

```mermaid
flowchart LR
    subgraph Input["Input"]
        A[Review]
        B[Preprocess]
    end
    
    subgraph Encode["Tokenize"]
        C[VisoBERT]
    end
    
    subgraph Models["STL Models"]
        D[AD Model<br/>11 Aspects]
        E[SC Model<br/>Focal Loss]
    end
    
    subgraph Out["Output"]
        F[Aspects]
        G[Sentiment<br/>Pos/Neg/Neu]
    end
    
    A --> B --> C
    C --> D --> F
    C --> E --> G
```

---

## 3. Sơ đồ Use Case

```mermaid
flowchart LR
    C((Khách hàng)) --> C1[Đăng nhập] & C2[Xem/Mua SP] & C3[Đánh giá]
    A((Admin)) --> A1[Quản lý SP/ĐH] & A2[Kiểm duyệt] & A3[Thống kê]
    
    C2 -.-> E1((MoMo))
    C3 -.-> E2((ABSA))
    A2 -.-> E2
```

---

## Hướng dẫn

1. Vào https://mermaid.live/
2. Paste code → Export PNG
3. Lưu: `1.png`, `6.png`, `2.png`

