
create or replace function public.is_admin()
returns boolean
language sql
stable
security definer
set search_path = public
as $$
  select exists (
    select 1
    from public.profiles
    where id = auth.uid()
    and is_admin = true
  );
$$;

alter table public.profiles enable row level security;
alter table public.deposits enable row level security;
alter table public.withdrawals enable row level security;
alter table public.purchases enable row level security;

create policy "read own profile"
on public.profiles for select
using (id = auth.uid() or public.is_admin());

create policy "read own deposits"
on public.deposits for select
using (user_id = auth.uid() or public.is_admin());

create policy "insert own deposits"
on public.deposits for insert
with check (user_id = auth.uid());

create policy "read own withdrawals"
on public.withdrawals for select
using (user_id = auth.uid() or public.is_admin());

create policy "insert own withdrawals"
on public.withdrawals for insert
with check (user_id = auth.uid());

create policy "read own purchases"
on public.purchases for select
using (user_id = auth.uid() or public.is_admin());

create policy "insert own purchases"
on public.purchases for insert
with check (user_id = auth.uid());
# superfast77
