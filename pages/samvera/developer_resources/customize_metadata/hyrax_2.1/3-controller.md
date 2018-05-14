---
title: "Understanding the Controller"
a-z: ["Understanding the Controller"]
keywords: Customize Metadata
categories: How to Do All the Things
permalink: customize-metadata-controller.html
folder: samvera/how-to/customize_metadata/hyrax_1.0/controller.md
sidebar: home_sidebar
tags: [development_resources]
version: 
  id: 'hyrax_1.0-stable'
---

<ul class='info'><li>This tutorial assumes that you generated a work type name GenericWork.  If you used a different name, substitute that name for all occurrences of GenericWork and generic_work.</li></ul>

The GenericWorksController class is generated with some default behaviors.  It is located at `app/controllers/hyrax/generic_works_controller.rb`

```ruby
# Generated via
#  `rails generate hyrax:work GenericWork`
module Hyrax
  # Generated controller for GenericWork
  class GenericWorksController < ApplicationController
    # Adds Hyrax behaviors to the controller.
    include Hyrax::WorksControllerBehavior
    include Hyrax::BreadcrumbsForWorks
    self.curation_concern_type = ::GenericWork

    # Use this line if you want to use a custom presenter
    self.show_presenter = Hyrax::GenericWorkPresenter
  end
end

```

As usual, you can add code for special processing to the controller.  The controller's main purpose is to connect it to other classes that define the model, set up forms, and display show pages.


## Files defined by the controller...

<ul class='info'><li>The default generated code is shown here.  You will see in later steps how you can modify these classes
to customize metadata.  The code here is just to introduce you to some of the files you will be modifying.</li></ul>


### Model class

The model class, which is part of the standard Rails Model-View-Controller, has its name determined by Rails convention.

`model_name = controller_name minus 'Controller'` (e.g. GenericWork)

```
# Generated via
#  `rails generate hyrax:work GenericWork`
class GenericWork < ActiveFedora::Base
  include ::Hyrax::WorkBehavior

  self.indexer = GenericWorkIndexer
  # Change this to restrict which works can be added as a child.
  # self.valid_child_concerns = []
  validates :title, presence: { message: 'Your work must have a title.' }

  # This must be included at the end, because it finalizes the metadata
  # schema (by adding accepts_nested_attributes)
  include ::Hyrax::BasicMetadata
end
```

### Form class

The form class is used to control how metadata appears on the new/edit work form.  A form class is created for each work type when the work type is generated.  The controller knows about this class through the `app/services/hyrax/work_form_service.rb` [form_class](https://github.com/samvera/hyrax/blob/v2.1.0.rc2/app/services/hyrax/work_form_service.rb#L7-L9) method.

Default: form_class = model's name + Form (e.g. GenericWorkForm)

```
# Generated via
#  `rails generate hyrax:work GenericWork`
module Hyrax
  # Generated form for GenericWork
  class GenericWorkForm < Hyrax::Forms::WorkForm
    self.model_class = ::GenericWork
    self.terms += [:resource_type]
  end
end
```

Modifying: You can change the class that is used as the form class by setting it in the controller.

```ruby
    self.form_class = GenericWorkExtForm # UNCOMMON, see note below.
```

<ul class='warning'><li> It is uncommon to set self.form_class as the form class is already generated (e.g. GenericWorkForm) and can hold your extensions.</li></ul>



### Presenter class

The presenter class is used to control how metadata appears on the work show page.  This class is NOT generated.  See [Modifying the Show Page](customize-metadata-show-page.html) for more information on creating a presenter class.  The default presenter class is defined in [works_controller_behavior.rb](https://github.com/samvera/hyrax/blob/master/app/controllers/concerns/hyrax/works_controller_behavior.rb)

Default: show_presenter = Hyrax::WorkShowPresenter

```
# Generated via
#  `rails generate hyrax:work DemoWork`
module Hyrax
  class DemoWorkPresenter < Hyrax::WorkShowPresenter
  end
end
```

Modifying: You can change the class that is used as the presenter class by setting it in the controller.

```ruby
    self.show_presenter = GenericWorkPresenter # UNCOMMON, see note below.
```

<ul class='warning'><li> It is uncommon to set self.show_presenter as the presenter class is already generated (e.g. GenericWorkPresenter) and can hold your extensions.</li></ul>

---

<p><a href="customize-metadata-labels.html"><button type="button" class="btn btn-primary">Prev: Labels and Help Text</button></a>  <a href="customize-metadata-model.html"><button type="button" class="btn btn-primary">Next: Defining Metadata in the Model</button></a></p>
