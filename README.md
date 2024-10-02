# Axum with SeaORM example app

1. 프로젝트 시작

```bash
cargo new [user directory]
```

2. 데이터 구조 마이그레이션

   가. 사전에 seaorm 관리 프로그램 설치한다.

```bash
cargo install seaorm-cli
```

    나. 테이블 관리를 위한 directory를 준비한다.

```bash
sea migrate init
```

    다. 테이블관리를 위한 기초 빈 템플레이트 프로그램 생성한다.

```bash
sea migrate  generate "CREATE TABLE user"
```

    라. 생성된 프로그램을 수정하여 원하는 테이블 형태로 만든다

```rust
// migration/src/ m20240930_051328_CREATE_TABLE_user.rs
use sea_orm_migration::{prelude::*, schema::*};

#[derive(DeriveMigrationName)]
pub struct Migration;

#[async_trait::async_trait]
impl MigrationTrait for Migration {
    async fn up(&self, manager: &SchemaManager) -> Result<(), DbErr> {
        // Replace the sample below with your own migration scripts
        ~~todo!();~~  제거 필수

        manager
            .create_table(
                Table::create()
                    .table(User::Table)
                    .if_not_exists()
                    .col(
                        ColumnDef::new(User::Id)
                            .integer()
                            .not_null()
                            .auto_increment()
                            .primary_key(),
                    )
                    .col(ColumnDef::new(User::Name).string().not_null())
                    .col(ColumnDef::new(User::Email).string().not_null())
                    .col(ColumnDef::new(User::Age).integer().not_null())
                    .col(ColumnDef::new(User::Birthday).date())
                    .to_owned(),
            )
            .await
    }

    async fn down(&self, manager: &SchemaManager) -> Result<(), DbErr> {
        // Replace the sample below with your own migration scripts
        ~~ todo!(); ~~       제거 필수

        manager
            .drop_table(Table::drop().table(User::Table).to_owned())
            .await
    }
}

#[derive(DeriveIden)]
enum User {
    Table,
    Id,
    Name,
    Email,
    Age,
    Birthday,
}
```

```rust
// migration/src/ lib.rs
pub use sea_orm_migration::prelude::*;

mod m20240930_051328_CREATE_TABLE_user;

pub struct Migrator;

#[async_trait::async_trait]
impl MigratorTrait for Migrator {
    fn migrations() -> Vec<Box<dyn MigrationTrait>> {
        vec![
            Box::new(m20240930_051328_CREATE_TABLE_user::Migration),
        ]
    }
}
```

마. seaorm cli에서 데이터베이스에 접속하기 위하여는 환경변수에 "DATABASE_URL"가 정의 되어야 한다. 다른 방법으로는 .env파일에 정의한다.
그리고, 명령어를 사용하여 테이블을 생성한다.

```bash
DATABASE_URL=postgres://postgres:password@localhost:5432/postgres sea migrate  up
```

바. 생성된 테이블을 이용하여 entity 구조체를 생성한다.

```bash
sea generate  entity -o ./entity
cd entity
cargo init
mv *.rs ./src
```

```rust

```

1. Modify the `DATABASE_URL` var in `.env` to point to your chosen database

1. Turn on the appropriate database feature for your chosen db in `service/Cargo.toml` (the `"sqlx-postgres",` line)

1. Execute `cargo run` to start the server

1. Visit [localhost:8000](http://localhost:8000) in browser

Run mock test on the service logic crate:

```bash
cd service
cargo test --features mock
```
