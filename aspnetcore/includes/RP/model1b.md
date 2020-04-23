<!-- THIS INCLUDE USED BY MVC AND RP -->
Добавьте в класс `Movie` следующие свойства:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Класс `Movie` содержит:

* Поле `ID` является обязательным для первичного ключа базы данных.
* `[DataType(DataType.Date)]`.  Атрибут [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) указывает тип данных (Date). С этим атрибутом:

  * пользователю не требуется вводить сведения о времени в поле даты.
  * Отображается только дата, а не время.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) рассматриваются в следующем руководстве.
