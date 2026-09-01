erDiagram
    %% 實體關係定義
    Employee ||--o| Accounts : "1:1 實體負責人"
    Accounts ||--o{ UserRoles : "1:N 派發"
    Roles ||--o{ UserRoles : "1:N 關聯"
    Roles ||--o{ RolePermissions : "1:N 授權"
    Permissions ||--o{ RolePermissions : "1:N 關聯"
    Permissions ||--o{ SystemNodes : "1:N 節點綁定"
    SystemNodes ||--o{ SystemNodes : "1:N 樹狀自關聯"

    %% 資料表與核心欄位定義 (已隱藏基礎審計欄位以凸顯架構重點)
    Employee {
        INT EmployeeID PK
        VARCHAR(20) EmployeeNo "業務鍵 (UQ)"
        TINYINT JobStatus "人事狀態(連動 IsActive)"
        BIT IsActive
    }

    Accounts {
        INT AccountID PK
        INT EmployeeID FK "邏輯聯動"
        VARCHAR(50) Username "過濾唯一索引 (Filtered UQ)"
        VARCHAR(255) PasswordHash "Identity V3 封裝"
        BIT IsLocked
        TINYINT FailedCount
        BIT IsActive
        TIMESTAMP RowVersion "樂觀鎖"
    }

    Roles {
        INT RoleID PK
        VARCHAR(50) RoleCode "UNIQUE"
        BIT IsSystem "系統內建防護"
        BIT IsActive
    }

    UserRoles {
        INT AccountID PK,FK "複合主鍵"
        INT RoleID PK,FK "複合主鍵"
    }

    Permissions {
        VARCHAR(100) PermissionCode PK "字串自然鍵"
        NVARCHAR(50) PermissionName
        BIT IsActive
    }

    RolePermissions {
        INT RoleID PK,FK "複合主鍵"
        VARCHAR(100) PermissionCode PK,FK "複合主鍵"
    }

    SystemNodes {
        INT NodeID PK
        TINYINT NodeType "1:模組, 2:頁面, 3:按鈕"
        INT ParentNodeID FK "自我參照 (樹狀結構)"
        VARCHAR(255) FormClassPath "UI 反射路由"
        VARCHAR(100) PermissionCode FK "允許 NULL (目錄節點)"
        BIT IsActive
    }
