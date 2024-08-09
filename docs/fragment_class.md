# Fragment Class

The UI5 Antares library includes a built-in class (FragmentCL) providing a set of public methods to load the contents of a fragment file (*.fragment.xml) and to open a dialog or a popover.

!!! info

    The [Custom Control From Fragment](./entry_create.md/#custom-control-from-fragment) and [Custom Content From Fragment](./entry_create.md/#custom-content-from-fragment) methods of the Entry classes also use the FragmentCL class.

## Constructor

In order to utilise the functionality of FragmentCL, it is necessary to initialise the object.

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
      <td>fragmentPath</td>
      <td><code>string</code></td>
      <td>Yes</td>
      <td></td>
      <td>The path of the fragment whose content will be loaded or opened</td>
    </tr>
    <tr>
      <td>openByControl?</td>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">sap.ui.core.Control</a></td>
      <td>No</td>
      <td></td>
      <td>If the loaded fragment contains a popover, this parameter determines which control the popover opens next to. For example: <a href="https://sapui5.hana.ondemand.com/sdk/#api/sap.m.Button">sap.m.Button</a></td>
    </tr>
  </tbody>
</table>

!!! example

    Let us assume that we have created a file with the extension **.fragment.xml** and the following content. Our objective is to open the dialog in the controller.

``` xml title="Dialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
                <Label text="Price" />
                <Slider
                    width="100%"
                    min="1000"
                    max="100000"
                    showAdvancedTooltip="true"
                    showHandleTooltip="true"
                    inputsAsTooltips="true"
                    enableTickmarks="true"
                    step="1000"
                    class="sapUiMediumMarginBottom"
                    value="{price}"
                />
                <Label text="Currency" />
                <ComboBox selectedKey="{currency}">
                    <items>
                        <core:Item
                            key="EUR"
                            text="Euro"
                        />
                        <core:Item
                            key="USD"
                            text="US Dollar"
                        />
                        <core:Item
                            key="TRY"
                            text="Turkish Lira"
                        />
                    </items>
                </ComboBox>
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseProductDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 14"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onOpenDialog() {
        // Initialize with the controller and fragment path
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onOpenDialog: async function () {
              // Initialize with the controller and fragment path
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");
            }
          });

        });
    ```

## Load Content

To load the content from a fragment file, the **load(): Promise&lt;Control \| Control[]&gt;** method can be utilized.

!!! info

    In the event that the fragment contains more than one UI control, the **load()** method will return an array containing the UI controls. Conversely, for single content, the **load()** method will return the UI control itself.

!!! danger "Attention"

    Please be aware that the **load()** method is **asynchronous** and must be awaited.

!!! example

    Let us assume that we have created a file with the extension **.fragment.xml** with the following content. We would now like to use the [sap.m.Text](https://sapui5.hana.ondemand.com/sdk/#/api/sap.m.Text) in the controller.

``` xml title="Text.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Text text="My Text" />
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 18 21"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class
    import Text from "sap/m/Text";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async onLoadFragment() {
        // Initialize with the controller and fragment path
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

        // load the content of the fragment
        const content = await fragment.load() as unknown as Text;

        // use the content
        console.log(content.getText());
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            onLoadFragment: async function () {
              // Initialize with the controller and fragment path
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

              // load the content of the fragment
              const content = await fragment.load();

              // use the content
              console.log(content.getText());
            }
          });

        });
    ```

## Open Dialog or Popover

There are two distinct methods for opening a dialog or popover from a fragment file.

!!! danger "Attention"

    Please be advised that both methods support only the following dialog and popover classes:

    - [sap.m.Dialog](https://sapui5.hana.ondemand.com/#/api/sap.m.Dialog)
    - [sap.m.Popover](https://sapui5.hana.ondemand.com/#/api/sap.m.Popover)
    - [sap.m.MessagePopover](https://sapui5.hana.ondemand.com/#/api/sap.m.MessagePopover)
    - [sap.m.ColorPalettePopover](https://sapui5.hana.ondemand.com/#/api/sap.m.ColorPalettePopover)
    - [sap.m.ResponsivePopover](https://sapui5.hana.ondemand.com/#/api/sap.m.ResponsivePopover)

!!! tip

    Both methods include an optional parameter named `viewDependent: boolean`. Should you wish to add the dialog or popover as a dependent to your view, please set this parameter to **true**.

### Open Sync

In order to utilise the **open()** method, it is essential to first execute the [load()](#load-content) method.

To utilize the **open()** method, it is essential to first execute the [load()](#load-content) method.

!!! info

    The **open()** method returns the dialog or popover loaded from a fragment using the [load()](#load-content) method.

!!! danger "Attention"

    When opening a popover, it is necessary to set the control that the popover opens next to. Please refer to the [constructor](#constructor) documentation for further information.

!!! example "Dialog Example"

    Let us assume that we have created a file with the extension **.fragment.xml** with the following content. We would now like to open the **dialog** in the controller.

``` xml title="Dialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseProductDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17 20"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async openDialog() {
        // Initialize with the controller and fragment path
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

        // load the content of the fragment
        await fragment.load();

        // open the dialog
        fragment.open();
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openDialog: async function () {
              // Initialize with the controller and fragment path
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

              // load the content of the fragment
              await fragment.load();

              // open the dialog
              fragment.open();
            }
          });

        });
    ```

!!! example "Popover Example"

    Let us assume that we have created a file with the extension **.fragment.xml** and the following content. We would now like to open the **popover** in the controller.

``` xml title="Popover.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Popover>
        <content>
            <Text text="Test" />
            <Button
                text="Finish"
                press="onFinish"
            />
        </content>
    </Popover>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 15 18 21"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class
    import Button from "sap/m/Button";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async openPopover() {
        // Initialize with the controller and fragment path and the open by control
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName", this.getView()?.byId("myButton") as Button);

        // load the content of the fragment
        await fragment.load();

        // open the dialog
        fragment.open();
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 21 24"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openPopover: async function () {
              // Initialize with the controller and fragment path and the open by control
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName", this.getView().byId("myButton"));

              // load the content of the fragment
              await fragment.load();

              // open the dialog
              fragment.open();
            }
          });

        });
    ```

### Open Async

The **openAsync()** method first executes the [load()](#load-content) method internally, and then opens the dialog or popover.

!!! info

    The **openAsync()** method returns the dialog or popover loaded from a fragment.

!!! danger "Attention"

    When opening a popover, it is necessary to set the control that the popover opens next to. Please refer to the [constructor](#constructor) documentation for further information.

!!! tip

    Please be aware that the **openAsync()** method is **asynchronous**. To retrieve the dialog or popover, please await the **openAsync()** method.

!!! example "Dialog Example"

    Let us assume that we have created a file with the extension **.fragment.xml** and the following content. We would now like to open the **dialog** in the controller.

``` xml title="Dialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseProductDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 17"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async openDialog() {
        // Initialize with the controller and fragment path
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

        // open the dialog
        fragment.openAsync();
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 21"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openDialog: async function () {
              // Initialize with the controller and fragment path
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

              // open the dialog
              fragment.openAsync();
            }
          });

        });
    ```

!!! example "Popover Example"

    Let us assume that we have created a file with the extension **.fragment.xml** and the following content. We would now like to open the **popover** in the controller.

``` xml title="Popover.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Popover>
        <content>
            <Text text="Test" />
            <Button
                text="Finish"
                press="onFinish"
            />
        </content>
    </Popover>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 3 15 18"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class
    import Button from "sap/m/Button";

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      public onInit() {

      }

      public async openPopover() {
        // Initialize with the controller and fragment path and the open by control
        const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName", this.getView()?.byId("myButton") as Button);

        // open the dialog
        fragment.openAsync();
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 21"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openPopover: async function () {
              // Initialize with the controller and fragment path and the open by control
              const fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName", this.getView().byId("myButton"));

              // open the dialog
              fragment.openAsync();
            }
          });

        });
    ```

## Close Dialog or Popover

To close the dialog or popover that was opened by any of the approaches listed below, you will need to use the **close()** or **closeAndDestroy()** method.

1) [load()](#load-content) and [open()](#open-sync)

2) [openAsync()](#open-async)

!!! danger "Attention"

    Please be advised that it is your responsibility to destroy the content after using the **close()** method. This can be achieved by using the [destroyFragmentContent()](#destroy-fragment-content) method. Alternatively, if you use the **closeAndDestroy()** method, the content will be destroyed automatically.

!!! example

    Let us assume that we have created a file with the extension **.fragment.xml** with the following content. We would now like to open the **dialog** in the controller. When the user presses the **Close** button, we will close the dialog and destroy it.

``` xml title="Dialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 8 16 19 22-25"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      private fragment: FragmentCL; // The class property

      public onInit() {

      }

      public async openDialog() {
        // Initialize with the controller and fragment path and set to the class property
        this.fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

        // open the dialog
        this.fragment.openAsync();
      }

      public async onCloseDialog () {
        this.fragment.close(); // close the dialog
        this.fragment.destroyFragmentContent(); // do not forget to destroy after closing
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 21 24-27"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openDialog: async function () {
              // Initialize with the controller and fragment path and set to the class property
              this.fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

              // open the dialog
              this.fragment.openAsync();
            },

            onCloseDialog: async function () {
              this.fragment.close(); // close the dialog
              this.fragment.destroyFragmentContent(); // do not forget to destroy after closing
            }
          });

        });
    ```

## Get Fragment Content

To retrieve the content loaded from a fragment file using the [load()](#load-content) or [openAsync()](#open-async) method, the **getFragmentContent()** method can be utilized.

<table>
  <thead>
    <tr>
      <th>Returns</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">Control</a> | <a href="https://sapui5.hana.ondemand.com/#/api/sap.ui.core.Control">Control[]</a></td>
      <td>Returns the control or controls loaded from a fragment file using the <a href="#load-content">load()</a> or <a href="#open-async">openAsync()</a> method</td>
    </tr>
  </tbody>
</table>

## Destroy Fragment Content

To destroy the content loaded from a fragment using the [load()](#load-content) or [openAsync()](#open-async) methods, the **destroyFragmentContent()** method can be utilized.

!!! danger "Attention"

    Should you wish to destroy the content of the dialog when the user presses the **ESC** button on the keyboard, we advise you to use the **setAutoDestroyOnESC(destroy: boolean)** method and set the parameter of the method to **true**. Otherwise, you will be responsible for destroying the content when the user presses the **ESC** button. Please note that if the content is not destroyed in each ESC event, the dialog cannot be loaded again.

!!! example

    Let us assume that we have created a file with the extension **.fragment.xml** and the following content. We would now like to open the **dialog** in the controller. When the user presses the **Close** button, we will close the dialog and destroy it.

``` xml title="Dialog.fragment.xml"
<core:FragmentDefinition
    xmlns:form="sap.ui.layout.form"
    xmlns="sap.m"
    xmlns:core="sap.ui.core"
>
    <Dialog title="Create New Product">
        <form:SimpleForm>
            <form:content>
                <Label text="Product ID" />
                <MaskInput
                    mask="CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCCC"
                    value="{ID}"
                    placeholderSymbol="_"
                >
                    <rules>
                        <MaskInputRule
                            maskFormatSymbol="C"
                            regex="[a-f0-9]"
                        />
                    </rules>
                </MaskInput>
                <Label text="Name" />
                <Input value="{name}" />
                <Label text="Description" />
                <TextArea
                    value="{description}"
                    rows="5"
                />
            </form:content>
        </form:SimpleForm>
        <beginButton>
            <Button
                text="Complete"
                press="onCompleteProduct"
            />
        </beginButton>
        <endButton>
            <Button
                text="Close"
                press="onCloseDialog"
            />
        </endButton>
    </Dialog>
</core:FragmentDefinition>
```

=== "TypeScript"

    ``` ts linenums="1" hl_lines="2 8 16 19 22-25"
    import Controller from "sap/ui/core/mvc/Controller";
    import FragmentCL from "ui5/antares/ui/FragmentCL"; // Import the class

    /**
     * @namespace your.apps.namespace
     */
    export default class YourController extends Controller {
      private fragment: FragmentCL; // The class property

      public onInit() {

      }

      public async openDialog() {
        // Initialize with the controller and fragment path and set to the class property
        this.fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

        // open the dialog
        this.fragment.openAsync();
      }

      public async onCloseDialog () {
        this.fragment.close(); // close the dialog
        this.fragment.destroyFragmentContent(); // do not forget to destroy after closing
      }
    }
    ```

=== "JavaScript"

    ``` js linenums="1" hl_lines="3 18 21 24-27"
    sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "ui5/antares/ui/FragmentCL" // Import the class
    ], 
        /**
         * @param {typeof sap.ui.core.mvc.Controller} Controller
         */
        function (Controller, FragmentCL) {
          "use strict";

          return Controller.extend("your.apps.namespace.YourController", {
            onInit: function () {

            },

            openDialog: async function () {
              // Initialize with the controller and fragment path and set to the class property
              this.fragment = new FragmentCL(this, "your.apps.namespace.fragments.FragmentFileName");

              // open the dialog
              this.fragment.openAsync();
            },

            onCloseDialog: async function () {
              this.fragment.close(); // close the dialog
              this.fragment.destroyFragmentContent(); // do not forget to destroy after closing
            }
          });

        });
    ```