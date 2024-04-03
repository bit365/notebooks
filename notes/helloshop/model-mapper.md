# 自动映射实体和模型


## 使用 AutoMapper 自动映射实体和模型

AutoMapper 是一个对象映射工具，可以自动映射实体对象和模型对象，减少手动映射的工作量，提高开发效率，除此之外 Mapster 也是一个高性能的对象映射工具，支持源生成代码，性能更好。AutoMapper 比 Mapster 更加流行，更加成熟，更加稳定，更加易用，更加灵活，更加强大，更加全面，更加受欢迎。AutoMapper 是 .NET Foundation 的一部分，是一个开源项目，是一个非常优秀的对象映射工具。

## 安装 NuGet 包

```shell
dotnet add package AutoMapper
```

## 在依赖注入中注册 AutoMapper

```csharp
builder.Services.AddAutoMapper(Assembly.GetExecutingAssembly());
```

## 创建映射配置文件

```csharp

public class MappingProfile : Profile
{
    public MappingProfile()
    {
        CreateMap<User, UserDto>();
        CreateMap<UserDto, User>();
    }
}

```

## 使用 AutoMapper 映射实体和模型

```csharp

public class UserService
{
    private readonly IMapper _mapper;

    public UserService(IMapper mapper)
    {
        _mapper = mapper;
    }

    public UserDto GetUser(int id)
    {
        var user = _dbContext.Users.Find(id);
        return _mapper.Map<UserDto>(user);
    }

    public void UpdateUser(UserDto userDto)
    {
        var user = _mapper.Map<User>(userDto);
        _dbContext.Users.Update(user);
        _dbContext.SaveChanges();
    }
}

```

## 分页模式

常见的分页模式有两种，一种是基于页码和页大小的分页模式，一种是基于游标和页大小的分页模式。