
run stuff in production

doesn’t generate stuff dynamically, expects things to be precompiled

rails doesn’t serve static files in production (nginx)

so if you want to test prod locally, change the config to serve static assets to true

you need a javascript runtime to compile coffeescript, use node

digest is for caching

use helpers to generate the asset locations so that the digests get generated

minimize js

gzip files

config assets compile, can set this to true if you want to compile on demand (like in dev) -> you’d also have to change application file to require the assets gems in the gemfile

you could also set config.assets.initialize_on_precompile -> don't load all of rails when precompiling

  


what is asset compiling?

coffee -> js

sass -> css

stitches everything into one css file and one js file

application manifest

  


what are problems that can happen?

order of assets wrong, one thing requires another thing not loaded yet

  


  


features

  * concatenation
  *     * web browsers can only make x parallel requests
  * minification or compression
  * higher level languages -> precompilation
  * fingerprinting
  *     * When a filename is unique and based on its content, HTTP headers can be set to encourage caches everywhere (whether at CDNs, at ISPs, in networking equipment, or in web browsers) to keep their own copy of the content. When the content is updated, the fingerprint will change. This will cause the remote clients to request a new copy of the content. This is generally known as *cache busting*.
    * <https://www.mnot.net/cache_docs/>
  * public
  *     * previous versions of rails had /public/images javascripts and stylesheets directories
    * now we put everything in the app/assets directory
    * when we precompile we’ll compile and move everything to public/assets and they are served statically by the webserver
  * resources specific javascripts
  *     * For example, if you generate a `ProjectsController`, Rails will also add a new file at `app/assets/javascripts/projects.js.coffee` and another at `app/assets/stylesheets/projects.css.scss`. By default these files will be ready to use by your application immediately using the `require_tree` directive. See [Manifest Files and Directives](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives)for more details on require_tree.

You can also opt to include controller specific stylesheets and JavaScript files only in their respective controllers using the following:

`<%= javascript_include_tag params[:controller] %>` or `<%= stylesheet_link_tag params[:controller] %>`

When doing this, ensure you are not using the `require_tree` directive, as that will result in your assets being included more than once.

  * organization
  *     * Pipeline assets can be placed inside an application in one of three locations: `app/assets`, `lib/assets` or `vendor/assets`.

    *       * `app/assets` is for assets that are owned by the application, such as custom images, JavaScript files or stylesheets.
      * `lib/assets` is for your own libraries' code that doesn't really fit into the scope of the application or those libraries which are shared across applications.
      * `vendor/assets` is for assets that are owned by outside entities, such as code for JavaScript plugins and CSS frameworks. Keep in mind that third party code with references to other files also processed by the asset Pipeline (images, stylesheets, etc.), will need to be rewritten to use helpers like `asset_path`.
      * Besides the standard `assets/*` paths, additional (fully qualified) paths can be added to the pipeline in `config/application.rb`. For example:

```config.assets.paths << Rails.root.join("lib","videoplayer","flash")```
  * manifest
    * `//= require jquery`
    * `//= require jquery_ujs`
    * `//= require_tree .`
  * Directives are processed top to bottom, but the order in which files are included by `require_tree` is unspecified. You should not rely on any particular order among those. If you need to ensure some particular JavaScript ends up above some other in the concatenated file, require the prerequisite file first in the manifest. Note that the family of `require` directives prevents files from being included twice in the output.
  
  * where is jquery?
  
  * Adding Assets to Your Gems
    * Assets can also come from external sources in the form of gems. A good example of this is the `jquery-rails` gem which comes with Rails as the standard JavaScript library gem. This gem contains an engine class which inherits from `Rails::Engine`. By doing this, Rails is informed that the directory for this gem may contain assets and the `app/assets`, `lib/assets` and `vendor/assets` directories of this engine are added to the search path of Sprockets.

  * what gets precompiled
    * ```[ Proc.new { |filename, path| path =~ /app\/assets/ && !%w(.js .css).include?(File.extname(filename)) },
/application.(css|js)$/ ]```
    * ```Rails.application.config.assets.precompile += ['admin.js', 'admin.css', 'swfObject.js']```

  * precompiling locally
    * In `config/environments/development.rb`, place the following line:
    * ```config.assets.prefix = "/dev-assets"```

The `prefix` change makes Sprockets use a different URL for serving assets in development mode, and pass all requests to Sprockets. The prefix is still set to `/assets` in the production environment. Without this change, the application would serve the precompiled assets from `/assets` in development, and you would not see any local changes until you compile assets again.

In practice, this will allow you to precompile locally, have those files in your working tree, and commit those files to source control when needed. Development mode will work as expected.

  
  

