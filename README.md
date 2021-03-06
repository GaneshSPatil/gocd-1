# gocd

[![Gem Version](https://badge.fury.io/rb/gocd.svg)](https://badge.fury.io/rb/gocd) [![CircleCI](https://circleci.com/gh/GaneshSPatil/gocd-1.svg?style=shield)](https://circleci.com/gh/GaneshSPatil/gocd-1)

### Installation
```ruby
gem 'gocd'
```

or

```bash
gem install gocd
```

### Code Documentation
http://www.rubydoc.info/gems/gocd/1.2.1

### Usage

```ruby
GOCD.server = GOCD::Server.new 'http://goserverurl.com'
GOCD.credentials = GOCD::Credentials.new 'username', 'password'
```

###### To check all pipelines:
```ruby
GOCD::AllPipelines.red_pipelines
```

###### To check a group of pipelines:

```ruby
pipelines = GOCD::PipelineGroup.new ['Pipeline1 :: stage1', 'Pipeline1 :: stage2', 'Pipeline2 :: stage1']
pipelines.red_pipelines
pipelines.status
pipelines.any_red?
```

#### Want to create your own gocd dashboard? Its easy now!
```ruby
require 'sinatra'
require 'gocd'

get '/' do
  GOCD.server = GOCD::Server.new 'http://goserverurl.com'
  GOCD.credentials = GOCD::Credentials.new 'username', 'password'
  GOCD::AllPipelines.red_pipelines.map {|pipeline| pipeline.to_hash}.to_json
end
```

#### Go Agents
###### To get all the idle agents:
```ruby
idle_agents = GOCD::Agents.idle
idle_agents.each { |agent| agent.name }
```

###### To get all the missing agents:
```ruby
GOCD::Agents.missing
```

###### To get all the disabled agents:
```ruby
GOCD::Agents.disabled
```

#### Pipeline Configuration

###### To get all the environments
```ruby
include GOCD::PIPELINE_CONFIG

environments #return all the environments and the whole hierarchy of it
jobs = environments.map(&:pipelines).flatten.map(&:stages).flatten.map(&:jobs).flatten
```

##### APIs provided by GOCD::PIPELINE_CONFIG
```ruby
module GOCD
  module PIPELINE_CONFIG
    def environments
    end

    def pipelines
    end

    def templates
    end

    def groups
    end

    def pipeline_config_response
    end
    
    class Environment
      def name
      end

      def pipelines
      end

      def pipeline_names
      end
    end

    class Pipeline
      def name
      end

      def stages
      end

      def template
      end

      def environment
      end
    end

    class Stage
      def name
      end

      def pipeline
      end

      def jobs
      end

      def environment
      end
    end

    class Job
      def name
      end

      def pipeline
      end

      def stage
      end

      def resources
      end

      def environment
      end
    end
  end
end
```

#### History Fetcher
You can fetch history of a job using the HistoryFetcher APIs
```ruby
require 'gocd'

GOCD.server = GOCD::Server.new 'http://goserverurl.com'
GOCD.credentials = GOCD::Credentials.new 'username', 'password'

include GOCD::PIPELINE_CONFIG

histories = []
runs = 1000
jobs = environments.map(&:pipelines).flatten.map(&:stages).flatten.map(&:jobs).flatten
jobs.each do |job|
  histories << GOCD::HistoryFetcher.fetch_job_history(job, runs)
end
```

LICENSE
-------

```LICENSE
Copyright (C) 2016 Ajit Singh

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
