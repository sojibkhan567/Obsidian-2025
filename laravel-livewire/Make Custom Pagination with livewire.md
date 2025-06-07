##### Make custom pagination with livewire

1. Make livewire component for get data with pagination (serve-side) 

```php
class UsersTable extends Component
{
    use WithPagination;

    public function render()
    {
        $users = User::paginate(10);
        return view('livewire.users-table', [
            "users" => $users
        ]);
    }
}
```

2. Now add pagination to blade file

```php
<!-- pagination -->
{{ $users->links('vendor.livewire.custom-pagination') }}
```

3. Create custom blade file in view folder (resources\views\vendor\livewire\custom-pagination.blade.php)

```php
<div class="flex justify-between items-center px-4 py-3">
    <div class="text-sm text-slate-500">
        Showing <b>{{ $paginator->firstItem() }} of {{ $paginator->lastItem() }}</b> of
        {{ $paginator->total() }}
    </div>
    @if ($paginator->hasPages())
        <div class="flex space-x-1">
            @if ($paginator->onFirstPage() === false)
                <button wire:click="previousPage" wire:loading.attr="disabled"
                    class="px-3 py-1 min-w-9 min-h-9 text-sm font-normal text-slate-500 bg-white border border-slate-200 rounded hover:bg-slate-50 hover:border-slate-400 transition duration-200 ease">
                    Prev
                </button>
            @endif
            {{-- {!! dd($elements) !!} --}}
            @foreach ($elements as $element)
                @if (is_array($element))
                    @foreach ($element as $page => $url)
                        <button wire:click="gotoPage({{ $page }})"
                            class="px-3 py-1 min-w-9 min-h-9 text-sm font-normal 
                            {{ $page == $paginator->currentPage() ? 'text-white bg-slate-800 border border-slate-800 rounded hover:bg-slate-600 hover:border-slate-600' : 'text-slate-500 bg-white border border-slate-200 rounded hover:bg-slate-50 hover:border-slate-400' }}
                               transition duration-200 ease">
                            {{ $page }}
                        </button>
                    @endforeach
                @endif
            @endforeach

            @if ($paginator->hasMorePages())
                <button wire:click="nextPage" wire:loading.attr="disabled"
                    class="px-3 py-1 min-w-9 min-h-9 text-sm font-normal text-slate-500 bg-white border border-slate-200 rounded hover:bg-slate-50 hover:border-slate-400 transition duration-200 ease">
                    Next
                </button>
            @endif
        </div>
    @endif
</div>

```