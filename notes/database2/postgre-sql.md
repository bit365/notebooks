# 学习并使用 PostgreSQL 数据库

##  为什么说 PostgreSQL 是最先进的关系型数据库？

1. 功能更强大：PostgreSQL具有更多高级功能，例如复杂查询、触发器和多版本并发控制等，这使得它更适合处理复杂的数据操作。

2. 数据一致性更高：PostgreSQL使用可靠的多版本并发控制系统，能够在高并发场景下保证数据的一致性和完整性。

3. 可扩展性更好：PostgreSQL支持更好的水平和垂直扩展，以满足各种规模的应用需求。

4. 安全性：PostgreSQL提供了访问控制和数据加密等安全特性，保护数据免受恶意攻击。

5. 备份和恢复：PostgreSQL具有强大的备份和恢复功能，能够恢复各种故障情况下的数据。

6. 免费和开源：PostgreSQL是一种免费和开源的数据库系统，用户可以自由地使用、修改和分发其源代码。

##  安装 PostgreSQL 和 pgAdmin

安装 PostgreSQL 和 pgAdmin 的方法有很多种，这里介绍一种比较简单的方法，学习使用 pgAdmin 管理 PostgreSQL 数据库，远程连接 PostgreSQL 数据库。

##  在 Visual Studio Code 中连接 PostgreSQL 数据库

在 Visual Studio Code 中连接 PostgreSQL 数据库，可以使用微软开发的 PostgreSQL 插件，也可以使用第三方开发的 PostgreSQL 插件。

##  在 Visual Studio Code 中使用 AI 助手编写 SQL 语句

Github Copilot 是一款由 OpenAI 开发的人工智能编程助手，它可以根据上下文提示程序员编写代码，目前支持 12 种编程语言，可以轻松实现 SQL 智能生成等。

##  将 SQL Server 数据库迁移到 PostgreSQL

 SQL Server 导入导出向导可以将 SQL Server 数据库迁移到 PostgreSQL 数据库，参见微软文档：

 [连接到 PostgreSQL 数据源（SQL Server 导入和导出向导）](https://learn.microsoft.com/zh-cn/sql/integration-services/import-export-data/connect-to-a-postgresql-data-source-sql-server-import-and-export-wizard)

 ##  使用 EF Core 连接 PostgreSQL 数据库

 Entity Framework Core 是一个轻量级、可扩展和开源的对象关系映射框架，它支持多种数据库，包括 SQL Server、MySQL、SQLite、PostgreSQL 代码如下：

 ```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;

namespace EFCoreDemo
{
    public class Program
    {
        public static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blogs)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseNpgsql(@"Host=localhost;Database=postgres;Username=postgres;Password=123456");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }

        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
 ```



