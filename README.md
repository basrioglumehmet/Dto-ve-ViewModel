
# Web API ile ViewModel ve DTO Kullanımı

Bu rehberde, C# Web API uygulamalarında ViewModel ve DTO kullanımı detaylı olarak anlatılmaktadır. Bu iki modelin kullanımı, uygulamanızın güvenliğini, performansını ve bakım kolaylığını artıracaktır.

## View Model Nedir?

View Model, kullanıcı arayüzüne (UI) gönderilecek veriyi tutan modeldir. Her UI için farklı View Model'ler tanımlanmalıdır. View Model'ler, veri güvenliğini sağlamak ve gereksiz bilgileri kullanıcıya göndermemek amacıyla kullanılır.

### Temel İlkeler
- **UI'ye Özel Olmalıdır**: Her kullanıcı arayüzü (UI) için ayrı bir View Model tanımlanmalıdır. Böylece farklı arayüzler, ihtiyaç duydukları spesifik veriyi alır.
- **API'den Geri Döndürülmemelidir**: View Model'ler API'den geri döndürülmemelidir. Bu, admin olmayan kullanıcıların hassas verilere erişimini engeller ve veri güvenliğini artırır.
- **Gereksiz Bilgiler Gönderilmemelidir**: Kullanıcıya gereksiz bilgi gönderilmemelidir. Bu, hem veri güvenliğini sağlar hem de veri trafiğini azaltır.
- **Veri Mapleme**: Veri maplemek için kullanılan modelle, UI'ye gönderilen model farklı olmalıdır. Aynı model kullanılırsa, gereksiz ve potansiyel olarak hassas veriler client tarafına indirilebilir, bu da büyük bir güvenlik riski oluşturur.

### Örnek

Aşağıdaki örnekte, `UserViewModel` sınıfı yalnızca UI için gerekli olan verileri içerir:

```csharp
public class UserViewModel
{
    public string Username { get; set; }
    public string Email { get; set; }
    // Yalnızca UI için gerekli alanlar burada yer alır
}
```

## DTO (Data Transfer Object) Nedir?

DTO, katmanlar arasında veri transferi için kullanılan modeldir. Genellikle veri tabanından gelen veriyi kaynak olarak kullanır ve uygulama içindeki farklı katmanlar arasında taşınmasını sağlar.

### Temel İlkeler
- **Katmanlar Arası Veri Transferi**: DTO'lar, uygulamanın farklı katmanları (örneğin, veri erişim katmanı ve iş mantığı katmanı) arasında veri taşımak için kullanılır.
- **Veri Kaynağı**: Genellikle veri tabanından gelen veri DTO'lar üzerinden taşınır. Bu, verinin işlenmesi ve taşınması sürecinde kolaylık sağlar.
- **Güvenlik ve Optimizasyon**: DTO'lar, yalnızca gerekli verileri içerir. Böylece, veri transferi sırasında gereksiz veri taşınmaz ve güvenlik riskleri minimize edilir.

### Örnek

Aşağıdaki örnekte, `UserDto` sınıfı veri transferi için gerekli olan verileri içerir:

```csharp
public class UserDto
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string Role { get; set; }
    // Yalnızca veri transferi için gerekli alanlar burada yer alır
}
```

## Detaylı Açıklama ve Kullanım

### View Model Kullanımı

#### Neden Kullanılır?
- **Güvenlik**: API'den geri döndürülmemesi gereken veriler, View Model'de saklanmaz. Bu, kullanıcıların hassas bilgilere erişmesini engeller.
- **Performans**: Gereksiz veri transferini önler, bu da performansı artırır ve ağ trafiğini azaltır.
- **Kullanıcı Deneyimi**: UI'ye özgü verilerle çalışmak, kullanıcı deneyimini iyileştirir.

#### Örnek Senaryo

Bir e-ticaret uygulamasında, kullanıcı profil sayfası için bir View Model oluşturulabilir. Bu model, yalnızca UI'de gösterilmesi gereken verileri içerir:

```csharp
public class UserProfileViewModel
{
    public string Username { get; set; }
    public string Email { get; set; }
    public string ProfilePictureUrl { get; set; }
    // Diğer UI'ye özgü alanlar
}
```

### DTO Kullanımı

#### Neden Kullanılır?
- **Katmanlar Arası İzolasyon**: DTO'lar, uygulama katmanları arasında veri taşırken, katmanların birbirlerinden bağımsız olmasını sağlar.
- **Veri Doğrulama ve Dönüşümü**: Veri transferi sırasında veri doğrulama ve dönüşüm işlemlerini kolaylaştırır.
- **Test Edilebilirlik**: DTO'lar, birim testleri ve entegrasyon testleri sırasında veri taşımayı kolaylaştırır.

#### Örnek Senaryo

Aynı e-ticaret uygulamasında, bir kullanıcı bilgilerini güncellemek için bir DTO kullanılabilir. Bu DTO, veri erişim katmanından iş mantığı katmanına veri taşırken kullanılır:

```csharp
public class UpdateUserDto
{
    public string Username { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
    // Diğer güncellenebilir alanlar
}
```

### Veri Mapleme

View Model'ler ve DTO'lar arasında veri mapleme işlemi, veri transferini ve dönüştürmeyi kolaylaştırır. Bu işlem genellikle AutoMapper gibi kütüphaneler kullanılarak yapılır.

#### AutoMapper Kullanımı

AutoMapper, nesneler arasında otomatik mapleme yapmayı sağlayan bir kütüphanedir. Aşağıdaki örnekte, `UserDto` ve `UserViewModel` arasında mapleme işlemi gösterilmektedir:

```csharp
// DTO
public class UserDto
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string Role { get; set; }
}

// View Model
public class UserViewModel
{
    public string Username { get; set; }
    public string Email { get; set; }
}

// AutoMapper Profili
public class UserProfile : Profile
{
    public UserProfile()
    {
        CreateMap<UserDto, UserViewModel>();
    }
}

// Kullanım
var config = new MapperConfiguration(cfg => cfg.AddProfile<UserProfile>());
var mapper = config.CreateMapper();

UserDto userDto = new UserDto { Id = 1, Username = "JohnDoe", Email = "john@example.com", Role = "Admin" };
UserViewModel userViewModel = mapper.Map<UserViewModel>(userDto);
```

## Web API Örneği

Aşağıda, Web API ile ViewModel ve DTO kullanımını gösteren bir örnek bulunmaktadır.

### Adım 1: Modelleri Tanımlama

```csharp
// UserDto.cs
public class UserDto
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string Role { get; set; }
}

// UserViewModel.cs
public class UserViewModel
{
    public string Username { get; set; }
    public string Email { get; set; }
}
```

### Adım 2: AutoMapper Profilini Tanımlama

```csharp
// UserProfile.cs
using AutoMapper;

public class UserProfile : Profile
{
    public UserProfile()
    {
        CreateMap<UserDto, UserViewModel>();
    }
}
```

### Adım 3: Web API Kontrolcüsü

```csharp
// UsersController.cs
using Microsoft.AspNetCore.Mvc;
using AutoMapper;
using System.Collections.Generic;

[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IMapper _mapper;

    public UsersController(IMapper mapper)
    {
        _mapper = mapper;
    }

    [HttpGet]
    public ActionResult<IEnumerable<UserViewModel>> GetUsers()
    {
        var users = new List<UserDto>
        {
            new UserDto { Id = 1, Username = "JohnDoe", Email = "john@example.com", Role = "Admin" },
            new UserDto { Id = 2, Username = "JaneDoe", Email = "jane@example.com", Role = "User" }
        };

        var userViewModels = _mapper.Map<IEnumerable<UserViewModel>>(users);
        return Ok(userViewModels);
    }
}
```

### Adım 4: Startup.cs'de AutoMapper'i Konfigüre Etme

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    services.AddAutoMapper(typeof(UserProfile));
}
```

Bu adımları takip ederek, C# Web API projenizde ViewModel ve DTO kullanarak veri güvenliğini sağlayabilir, performansı artırabilir ve bakım kolaylığı sağlayabilirsiniz.
