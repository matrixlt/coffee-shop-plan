
# Tech Note

## Entity - Facade - EJB - ManagedBean Structure

- this defines the way to find data out of DB and to store data to DB

## RESTful Web Service

- this allows us to show photo through url

## JSF and PrimeFaces

- this contributes our pages

## Java EE 8 Security Feature

- this promises the management of security

## Other techs or features

- use sass to write css conveniently
- use maven to manage dependency and to build the project
- use Git to manage code
- use component and template when some code may be use more then once to reuse code
- choose responsive layout and components to ensure the page is responsive

# Program Pattern

## retrive data from DB and show data on page

- index.xhtml (JFS page with primeface)

```xhtml
<ui:repeat var="product" value# {seasonSpecialContorller.specials}">
...
<components:productSummary product="#{product}"/>
...
```

- SeasonSpecialContorller.java (ManagedBean)

```java
public List<Product> getSpecials() {
    return seasonSpecialManager.getAllSeasonSpecial();
}
```

- SeasonSpecialManagerBean.java (EJB)

```java
public List<Product> getAllSeasonSpecial() {
    return seasonSpecialFacade.findAll().stream().map(
            (special) -> special.getProduct()
    ).collect(Collectors.toList());
}
```

- SeasonSpecialFacade.java (SessionBean)

```java
public List<T> findAll() {
    CriteriaQuery<T> cq = getEntityManager().getCriteriaBuilder().createQuery(entityClass);
    cq.select(cq.from(entityClass));
    return getEntityManager().createQuery(cq).getResultList();
    }
```

## get user's input and add data to DB

- manage-user.xhtml (JSF page)

```xhtml
<p:inputText id="admin-add-user-username" value="#{adminAddUserController.username}"
                                         type="text" required="true"/>
```

- AdminAddUserController.java (ManagedBean)

```java
public void addUser(){
    .....;
    userManager.addCustomer(username, password, nickname);
    .....;
}
```

- UserManager.java (EJB)

```java
public UserInfo addCustomer{
    UserInfo newUser = new UserInfo(null, username, new Date(),
        passwordHash.generate(password.toCharArray()), "customer");
        Customer customer = new Customer(null, nickname);
        customer.setUserInfo(newUser);
        newUser.setCustomer(customer);
        userInfoFacade.create(newUser);
        customerFacade.create(customer);
}
```

- UserInfoFacade.java (SessionBean)

```java
public void create(T entity) {
    getEntityManager().persist(entity);
}
```

## use JAX-RS

- productImage.xhtml

```xhtml
        <p:graphicImage styleClass="product-image"
                        url="#{bundle['Ui.Config.ImageResourcePath']}/#{cc.attrs.product.imageUuid.uuid}" />
```

- ImagesResource.java (Root Resource Class)

```java
@Path("images")
public class ImagesResource {

    @EJB
    private ImageFacade imageFacade;

    @GET
    @Path("{uuid}")
public Response getJpgImage(@PathParam("uuid") String uuid) {
    Image image = imageFacade.find(uuid);
    if (image == null) {
        throw new WebApplicationException(Status.NOT_FOUND);
    }
    ResponseBuilder rb = Response.ok(image.getContent(),   image.getMediaType());
    return rb.build();
}

}
```

## web page with features

- EL Expression, component, ajax, ...

```xhtml
<p:panel header="${bundle['Ui.Admin.SeasonSpecial.Title']}" id="season-special">
    <h:form>
        <components:selectProduct list="#{seasonSpecialContorller.specials}"
                selected="#{seasonSpecialContorller.selectedSeasonSpecial}">
            <f:facet name="actionButton">
                <p:commandButton icon="fa fa-remove" update="season-special"
                action="#{seasonSpecialContorller.removeSeasonSpecial}"
                value="#{bundle['Ui.Admin.SeasonSpecial.Button.Remove']}"/>
            </f:facet>
        </components:selectProduct>
        <components:selectProduct list="#{seasonSpecialContorller.allProductExceptSeasonSpecial}"
            selected="#{seasonSpecialContorller.selectedProduct}">
            <f:facet name="actionButton">
                <p:commandButton icon="fa fa-plus" update="season-special"
                action="#{seasonSpecialContorller.addSeasonSpecial}" type="submit"
                value="#{bundle['Ui.Admin.SeasonSpecial.Button.Add']}" />
            </f:facet>
        </components:selectProduct>
    </h:form>
</p:panel>
```

## security

- LoginController.java

```java
public String login() throws UserManagerException{
    Credential credential = new UsernamePasswordCredential(username, new Password(password));
        AuthenticationStatus status = securityContext.authenticate(
                getRequest(),
                getResponse(),
                AuthenticationParameters.withParams()
                        .credential(credential));
    switch(status){
        ...
    }
}
```

## use converter

- ProductConverter.java

```java
public Object getAsObject(FacesContext context, UIComponent component, String value){
    int id = Integer.parseInt(value);
    Product product = productManager.getProductById(id);
    return product;
}
```

```xhtml
<p:selectOneListbox  value="#{cc.attrs.selected}" converter="#{productConverter}" var="p"
                    filter="true" filterMatchMode="contains"
                    styleClass="select-one-list select-one-list-product">
    <f:selectItems value="#{cc.attrs.list}" var="product"
                itemLabel="#{product.name}"
                itemValue="#{product}" />
    <p:column>
        <h:outputText  value="#{p.name}" />
    </p:column>
</p:selectOneListbox>
```