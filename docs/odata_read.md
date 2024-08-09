# OData Read

[ODATA_MODEL_URL]: https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.v2.ODataModel

The ODataReadCL class leverages the [sap.ui.model.odata.v2.ODataModel][ODATA_MODEL_URL] to facilitate the handling of GET (READ) requests in a promisified manner.

## Constructor

In order to utilise the functionality of ODataReadCL, it is necessary to initialise the object.

<table>
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Type</th>
      <th>Mandatory</th>
      <th>Default Value</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>controller</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.mvc.Controller">sap.ui.core.mvc.Controller</a></td>
      <td>Yes</td>
      <td></td>
      <td>The controller object (usually <code>this</code> object)</td>
    </tr>
    <tr>
      <td>entityPath</td>
      <td><code>string</code></td>
      <td>Yes</td>
      <td></td>
      <td>The name of the <strong>EntitySet</strong>. It can start with a <strong>"/"</strong> (slash)</td>
    </tr>
    <tr>
      <td>modelName?</td>
      <td><code>string</code></td>
      <td>No</td>
      <td><code>undefined</code></td>
      <td>The name of the OData V2 model which can be found on the manifest.json file. <strong>Leave this parameter undefined</strong> if the name of the OData model = "" (empty string)</td>
    </tr>
  </tbody>
</table>

!!! tip "Tip for TypeScript"

    The **ODataReadCL&lt;EntityT, EntityKeysT&gt;** is a generic class and can be initialized with 2 types.

    - The `EntityT` type contains **all** properties of the `EntitySet` that is specified on the class constructor.
    - The `EntityKeysT` type contains the **key** properties of the `EntitySet` that is specified on the class constructor.

    The `EntityT` type is used as the returning type of the **read(): Promise&lt;EntityT[]&gt;** and **readByKey(keys: EntityKeysT): Promise&lt;EntityT&gt;** methods.

    The `EntityKeysT` type is used as the type for the `keys` parameter of the **readByKey(keys: EntityKeysT): Promise&lt;EntityT&gt;** method.

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14 19 24"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProduct() {
        // Initialize without a type
        const odata = new ODataReadCL(this, "Products"); 
      }

      public async onReadCategory() {
        // Initialize with a type
        const odata = new ODataReadCL<ICategory, ICategoryKeys>(this, "Categories"); 
      }

      public async onReadCustomer() {
        // Initialize with a model name
        const odata = new ODataReadCL(this, "Customers", "myODataModelName"); 
      }
    }

    interface ICategory {
      ID: string;
      name: string;
    }

    interface ICategoryKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 23"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 
            },

            onReadCategory: async function () {
              // Initialize with a model name
              const odata = new ODataReadCL(this, "Categories", "myODataModelName");
            }
          });

        });
    ```

## Read Request (GET EntitySet)

To send a GET (READ) request through the OData V2 model, you can use the **read(): Promise&lt;EntityT[]&gt;** method.

!!! info

    - The **read()** method runs **asynchronously** and can be awaited. 
    - If the request is successful, the **read()** method will return the data (multiple) of the target entity.

!!! tip

    By default, the [read()](https://sapui5.hana.ondemand.com/#/api/sap.ui.model.odata.v2.ODataModel%23methods/read) method of the [sap.ui.model.odata.v2.ODataModel][ODATA_MODEL_URL] class returns an object that contains a single property named **results**. However, the [OData Read](#odata-read) class destructs the original object and returns the value of the **results** property which is an array containing the data of the `EntitySet`. The return type is derived from the generic `EntityT` type.

!!! warning

    In the event of a failed GET request, the [OData Read](#odata-read) class will generate an error message. To ensure the error is identified and addressed, it is recommended to call the **read()** method within a **try-catch** block.

### Error Type

In the event of a failed GET request, the object generated by the class can contain the properties outlined below.

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;headers?: <code>object</code> | <code>undefined</code></td>
      <td>The HTTP response headers.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;message?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response message.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;responseText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response text.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusCode?: <code>string</code> | <code>number</code> | <code>undefined</code></td>
      <td>The status code of the HTTP request.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP status text.</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 18-28"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProducts() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 

        try {
          // send the http request and get the result.
          const result = await odata.read();

          result.forEach((row: IProducts) => {
            // write your logic
          });
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
        }
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string | null;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string | null;
      supplierID: string | null;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21-31"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProducts: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 

              try {
                // send the http request and get the result.
                const result = await odata.read();

                result.forEach((row) => {
                  // write your logic
                });
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```

## Read By Key Request (GET Entity)

To send a GET (READ) request to retrieve a single data through the OData V2 model, you can use the **readByKey(keys: EntityKeysT): Promise&lt;EntityT&gt;** method.

!!! info

    - The **readByKey()** method runs **asynchronously** and can be awaited. 
    - If the request is successful, the **readByKey()** method will return the data (single) of the target entity.

!!! warning

    In the event of a failed GET request, the [OData Read](#odata-read) class will generate an error message. To ensure the error is identified and addressed, it is recommended to call the **readByKey()** method within a **try-catch** block. 

### Error Type

In the event of a failed GET request, the object generated by the class can contain the properties outlined below.

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;headers?: <code>object</code> | <code>undefined</code></td>
      <td>The HTTP response headers.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;message?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response message.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;responseText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP response text.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusCode?: <code>string</code> | <code>number</code> | <code>undefined</code></td>
      <td>The status code of the HTTP request.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP status text.</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 18-28"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadSingleProduct() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 

        try {
          // send the http request and get the result. Note: you need to specify the key values of the entity to read a single data
          const result = await odata.readByKey({
            ID: "f60481d8-3426-4a91-a6a3-5e445a7deb46"
          });

          MessageBox.information("Product with ID: " + result.ID);
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
        }
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string | null;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string | null;
      supplierID: string | null;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21-31"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 

              try {
                // send the http request and get the result. Note: you need to specify the key values of the entity to read a single data
                const result = await odata.readByKey({
                  ID: "f60481d8-3426-4a91-a6a3-5e445a7deb46"
                });

                MessageBox.information("Product with ID: " + result.ID);
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```

## URL Parameters

Prior to sending the GET request with the [read()](#read-request-get-entityset) or [readByKey()](#read-by-key-request-get-entity) method, it is possible to set the URL parameters using the **setUrlParameters()** method.

=== "Setter (setUrlParameters)"

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>urlParameters</td>
          <td><code>Record&lt;string, string&gt;</code></td>
          <td>Yes</td>
          <td>The URL parameters of the GET request</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getUrlParameters)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><code>Record&lt;string, string&gt; | undefined</code></td>
          <td>Returns the value that was set using <strong>setUrlParameters()</strong> method. Default value is <strong>undefined</strong></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17-19"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProduct() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 

        // set the url parameters
        odata.setUrlParameters({
          "$expand": "toProductLocations"
        });
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21-23"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 

              // set the url parameters
              odata.setUrlParameters({
                "$expand": "toProductLocations"
              });         
            }
          });

        });
    ```

## Filters

To execute the [read()](#read-request-get-entityset) method with filters, two different methods are available.

=== "Setter (addFilter)"

    This method enables the user to add filters individually.

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>filter</td>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Filter">Filter</a></td>
          <td>Yes</td>
          <td>The filter object</td>
        </tr>
      </tbody>
    </table>

=== "Setter (setFilters)"

    This method enables users to apply multiple filters simultaneously.

    !!! danger "Attention"

        This method overwrites all the filters that have been added with the **addFilter()** method or set with the **setFilters()** method.

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>filters</td>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Filter">Filter[]</a></td>
          <td>Yes</td>
          <td>The array of filter objects</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getFilters)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Filter">Filter[]</a></td>
          <td>Returns all the filters that were added using the <strong>addFilter()</strong> method or set using the <strong>setFilters()</strong> method. Default value is <code>[]</code></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 4 17 18 21 22 28 29 32"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class
    import Filter from "sap/ui/model/Filter"; // Import Filter class
    import FilterOperator from "sap/ui/model/FilterOperator"; // Import FilterOperator enum

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProduct() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 
        const nameFilter = new Filter("name", FilterOperator.Contains, "smartphone");
        const priceFilter = new Filter("price", FilterOperator.GT, 1500);

        // add the filters one by one
        odata.addFilter(nameFilter);
        odata.addFilter(priceFilter);
      }

      public async onReadCategory() {
        // Initialize
        const odata = new ODataReadCL(this, "Categories"); 
        const nameFilter = new Filter("name", FilterOperator.Contains, "smartphone");
        const priceFilter = new Filter("price", FilterOperator.GT, 1500);

        // set the filters at once
        odata.setFilters([nameFilter, priceFilter]);
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 4 5 21 22 25 26 32 33 36"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL", // Import the class
        "sap/ui/model/Filter", // Import Filter class
        "sap/ui/model/FilterOperator" // Import FilterOperator enum
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL, Filter, FilterOperator) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 
              const nameFilter = new Filter("name", FilterOperator.Contains, "smartphone");
              const priceFilter = new Filter("price", FilterOperator.GT, 1500);

              // add the filters one by one
              odata.addFilter(nameFilter);
              odata.addFilter(priceFilter);        
            },

            onReadCategory: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Categories"); 
              const nameFilter = new Filter("name", FilterOperator.Contains, "smartphone");
              const priceFilter = new Filter("price", FilterOperator.GT, 1500);

              // set the filters at once
              odata.setFilters([nameFilter, priceFilter]);          
            }
          });

        });
    ```

## Sorters

To execute the [read()](#read-request-get-entityset) method with sorters, two different methods are available.

=== "Setter (addSorter)"

    This method enables the user to add sorters individually.

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>sorter</td>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Sorter">Sorter</a></td>
          <td>Yes</td>
          <td>The sorter object</td>
        </tr>
      </tbody>
    </table>

=== "Setter (setSorters)"

    This method enables users to apply multiple sorters simultaneously.

    !!! danger "Attention"

        This method overwrites all the sorters that have been added with the **addSorter()** method or set with the **setSorters()** method.

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Type</th>
          <th>Mandatory</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>sorters</td>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Sorter">Sorter[]</a></td>
          <td>Yes</td>
          <td>The array of sorter objects</td>
        </tr>
      </tbody>
    </table>

=== "Getter (getSorters)"

    <table>
      <thead>
        <tr>
          <th>Returns</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.model.Sorter">Sorter[]</a></td>
          <td>Returns all the sorters that were added using the <strong>addSorter()</strong> method or set using the <strong>setSorters()</strong> method. Default value is <code>[]</code></td>
        </tr>
      </tbody>
    </table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 16 17 20 21 27 28 31"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class
    import Sorter from "sap/ui/model/Sorter"; // Import the Sorter class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProduct() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 
        const nameSorter = new Sorter("name");
        const priceSorter = new Sorter("price", true); // descending

        // add the sorters one by one
        odata.addSorter(nameSorter);
        odata.addSorter(priceSorter);
      }

      public async onReadCategory() {
        // Initialize
        const odata = new ODataReadCL(this, "Categories"); 
        const nameSorter = new Sorter("name");
        const priceSorter = new Sorter("price", true); // descending

        // set the sorters at once
        odata.setSorters([nameSorter, priceSorter]);
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 4 20 21 24 25 31 32 35"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL", // Import the class
        "sap/ui/model/Sorter" // Import the Sorter class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL, Sorter) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 
              const nameSorter = new Sorter("name");
              const priceSorter = new Sorter("price", true); // descending

              // add the sorters one by one
              odata.addSorter(nameSorter);
              odata.addSorter(priceSorter);      
            },

            onReadCategory: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Categories"); 
              const nameSorter = new Sorter("name");
              const priceSorter = new Sorter("price", true); // descending

              // set the sorters at once
              odata.setSorters([nameSorter, priceSorter]);         
            }
          });

        });
    ```

## Additional Response Info

The [read()](#read-request-get-entityset) and the [readByKey()](#read-by-key-request-get-entity) methods return the data of the target entity. However, you may require further information such as the `status code` and `headers`.

Once the [read()](#read-request-get-entityset) or the [readByKey()](#read-by-key-request-get-entity) function has been completed, the **getResponse()** method can be utilized to obtain further details.

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>object</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;$reported?: <code>boolean</code> | <code>undefined</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;body?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP body</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;headers?: <code>object</code> | <code>undefined</code></td>
      <td>The HTTP response headers.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusCode?: <code>string</code> | <code>number</code> | <code>undefined</code></td>
      <td>The status code of the HTTP request.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;statusText?: <code>string</code> | <code>undefined</code></td>
      <td>The HTTP status text.</td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;_imported?: <code>boolean</code> | <code>undefined</code></td>
      <td></td>
    </tr>
    <tr>
      <td>&nbsp;&nbsp;&nbsp;&nbsp;data?: <code>{ results: EntityT[] }</code> | <code>undefined</code></td>
      <td>The data that was fetched</td>
    </tr>
  </tbody>
</table>

**Example**

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 27 29-31"
    import Controller from "sap/ui/core/mvc/Controller";
    import ODataReadCL from "ui5/antares/odata/v2/ODataReadCL"; // Import the class
    import { IError } from "ui5/antares/types/common"; // Import the error type
    import MessageBox from "sap/m/MessageBox";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onReadProduct() {
        // Initialize with a type
        const odata = new ODataReadCL<IProducts, IProductKeys>(this, "Products"); 

        try {
          // send the http request and get the result
          const result = await odata.readByKey({
            ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
          });

          MessageBox.information(result.ID + " was fetched.");

          // get the additional response info
          const response = odata.getResponse();

          if (response) {
            console.log("Status Code: " + response.statusCode);
          }
        } catch (error) {
          // catch the error
          MessageBox.error((error as IError).message || "Request failed");
        }
      }

    }

    interface IProducts {
      ID: string;
      name: string;
      description: string;
      brand: string;
      price: number;
      currency: string;
      quantityInStock: number;
      categoryID: string;
      supplierID: string;
    }

    interface IProductKeys {
      ID: string;
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 30 32-34"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/odata/v2/ODataReadCL", // Import the class
        "sap/m/MessageBox"
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, ODataReadCL, MessageBox) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onReadProduct: async function () {
              // Initialize
              const odata = new ODataReadCL(this, "Products"); 

              try {
                // send the http request and get the result
                const result = await odata.readByKey({
                  ID: "3ccb5dd2-cc12-483a-b569-a6ec844f8f0b"
                });

                MessageBox.information(result.ID + " was fetched.");

                // get the additional response info
                const response = odata.getResponse();

                if (response) {
                  console.log("Status Code: " + response.statusCode);
                }            
              } catch (error) {
                // catch the error
                MessageBox.error(error.message || "Request failed");
              }          
            }
          });

        });
    ```