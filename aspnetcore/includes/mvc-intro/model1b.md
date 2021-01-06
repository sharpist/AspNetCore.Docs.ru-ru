Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `Id`, которое является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`:  Атрибут [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) указывает тип данных (`Date`). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.