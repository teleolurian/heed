_       = require 'lodash'
exec    = require('child_process').exec
fs      = require 'fs'
path    = require 'path'
rm      = require('rimraf').sync

PATHS = root: path.resolve('.'), bin: path.resolve('./node_modules/.bin')
PATHS.targets = PATHS.root + '/targets'
PATHS.build   = PATHS.targets + '/build'
PATHS.ws      = PATHS.root + '/workspace'

dir = (k, s = '') ->
  s = "/#{s}" unless s.length == 0 or s.substr(0,1) == '/'
  (PATHS[k] ? PATHS.root) + s

refresh = (args...) ->
  rm dir(args...)
  fs.mkdirSync dir(args...)

task 'build', 'Build App', ->
  invoke 'compile:assets'
  exec "cp #{PATHS.ws}/index.html #{PATHS.build}"
  exec "cp ./package.json #{PATHS.build}"


task 'compile', 'Build Executables', ->
  refresh 'targets', 'bin'
  invoke 'build'
  NwBuilder = require 'node-webkit-builder'
  nw = new NwBuilder
    files:        dir 'build', '**/**'
    platforms:    ['osx64']
    buildDir:     PATHS.targets + '/bin'
    cacheDir:     PATHS.targets + '/cache'
  nw.on 'log', console.log
  nw.build().then -> console.log 'done'
    .catch console.error

task 'compile:assets', 'Build assets', ->
  invoke 'compile:assets:js'
  invoke 'compile:assets:css'
  invoke 'compile:assets:images'

task 'compile:assets:js', 'Build js from coffee', ->
  refresh 'build', 'js'
  exec "cp ./node_modules/jquery/dist/jquery.min.js #{dir 'build', 'js/jquery.min.js'}"
  exec "coffee -c -o #{dir 'build', 'js'} #{dir 'ws', 'scripts'}"


task 'compile:assets:css', 'Build CSS from stylus', ->
  refresh 'build', 'styles'
  exec dir('bin', 'stylus') + " -o #{dir 'build','styles'} #{dir 'ws', 'styles/main.styl'}"

task 'compile:assets:images', 'Build images from stylus', ->
  refresh 'build', 'images'
  exec "cp -r #{dir 'ws', 'images/*'} #{dir 'build', 'images'}"

task 'run', 'Run Application In Build', ->
  invoke 'build'
  exec "./node_modules/.bin/nw #{PATHS.build}"
