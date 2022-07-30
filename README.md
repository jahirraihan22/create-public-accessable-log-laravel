# create-public-accessable-log-laravel


### create class For logging

      
    <?php
      declare(strict_types=1);

      namespace App\Services\Logs;

      use Illuminate\Support\Facades\Storage;

      class CreateLog
      {
        protected $path;
        protected $file;

        /**
         *
         * @param path string
         * file path
         * where you want to create logs for a certain directory
         *
         */
        public function __construct(string $path)
        {
          $this->path = $path;
        }

        /**
         *
         * @param file_name string
         * file name which you want to save
         *
         * @param data array
         *
         *
         */
        public function create(string $file_name, array $data)
        {
          $this->file = $this->path . '/' . $file_name . '.json';
          if (Storage::disk('logs')->exists($this->file)) {
            $this->update($data);
          } else {
            $this->store([$data]);
          }
        }

        /**
         * @param data array or collection
         * to store data in storage path
         */
        private function store($data)
        {
          Storage::disk('logs')->put($this->file, json_encode($data, 1));
        }

        private function update($new_data)
        {
          // dd($new_data);
          $existing_data =  json_decode(Storage::disk('logs')->get($this->file));
          array_push($existing_data, (object) $new_data);
          $this->store($existing_data);
        }

        /**
         * @param file_name string
         * file you want to fetch;
         */
        public function fetch(string $file_name)
        {
          $this->file = $this->path . '/' . $file_name;
          try {
            return Storage::disk('logs')->get($this->setFileName($file_name));
          } catch (\Throwable $th) {
            return null;
          }
        }

        protected function setFileName($file_name)
        {
          return $this->file = $this->path . '/' . $file_name;
        }
      }
      
      
 ### create new storage disk in ***config/filesystem.php***
 
    disks => [
      ...
        'logs' => [
            'driver' => 'local',
            'root'   => public_path() . '/log',
        ],
      ...
    
    ]
    
    
### run this command to clear config
    
    php artisan config:cache
 
